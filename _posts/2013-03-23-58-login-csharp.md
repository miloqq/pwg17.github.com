---
layout: post
title: 58同城登录 c#，非直接操作js
description: "58同城登录 c#，非直接操作js"
category: "c#"
tags: ["58同城登录"]
---



很久没分析登录过程了，一同学在群里说58登录卡在了哪步哪步，他js加密方式，登录验证码都破解了。

google搜索了一遍，全是直接操作js的方式，这种方式不是很好，于是决定帮忙研究下。

58登录是挺复杂的，不过它不是加密方式复杂，而是在于你如果是用惯性思维去分析和写程序的话，会发现怎么都不对。

平时都是收集各个登录过程中的cookie，然后update domain发送过去最后一步，一般都差不多了。但58是严格监控客户端发送來的cookie的，若把某个不相关的cookie也发送给它是会失败的。


下面上分析流程：


登录页是：http://passport.58.com/login

往这里post：http://passport.58.com/dounionlogin

post的参数是：


isweak=0&path={0}&p1={1}&p2={2}&timesign={3}&ptk={4}&cd={5}&username={6}&password=password


![alt text](/assets/img/58from.png "表单参数")  


http://passport.58.com/login 页面上可以获取到的是isweak,path,ptk,cd.


p1,p2,timesign是js动态生成的。


在网站所有js文件里面查找，可以找到p1,p2,timesign的生成方式，这里必须介绍下firefox下面的yslow，配合firebug反解密网站的加密方式的神器。


![alt text](/assets/img/58timesign.png "timesign")


还用到一个timespan变量，继续找：


![alt text](/assets/img/58timespan.png "timespan")


var timespan = 1364016321210 - new Date().getTime();
var timesign = new Date().getTime() + timespan;

1364016321210 是服务器时间，应该是用来计算登录过程用了多长时间的。

接着找p1和p2的生成方式：


![alt text](/assets/img/58p1p2.png "p1&p2")


继续查找getm32str和getm16str这两个函数：



{% highlight javascript %}


    function getm32str(c, a) {

        if (a.length != 13) {
            alert("timesign error !!!");
            return "";
        }
        
        return hex_md5(hex_md5(c) + a.substring(5, 11));
    }

    function getm16str(c, a) {

        if (a.length != 13) {
            alert("timesign error !!!");
            return "";
        }
    
        return hex_md5(hex_md5_16(c) + a.substring(5, 11));
    }

{% endhighlight %}


先测试下hex_md5是不是标准的MD5函数，直接在firebug里面执行hex_md5('123456')：


![alt text](/assets/img/58p1p2.png "p1&p2")


结果是“e10adc3949ba59abbe56e057f20f883e”，是标准的md5，那就简单了，省去很多功夫。继续找下hex_md5_16函数的实现：


{% highlight javascript %}

    function hex_md5_16(c) {
        var a = hex_md5(c);
        a = a.substring(8, 24);
        return reverse(a);
    }

{% endhighlight %}


嗯，也没多复杂，md5之后从第8位开始截取16位字符，然后反转。

分析到这里，p1和p2的生成方式也出来了，ok，到这里可以写程序测试下了。

把上面需要的参数整理出来，往http://passport.58.com/dounionlogin POST过去。


![alt text](/assets/img/58post.png "post")


post过去之后返回www58com，58cooper，pptlogin，58passport，PPU 五个cookie，然后跳转到http://my.58.com/ ，整个登录过程就完成了。

陷阱就在这里，如果把58passport也带上去访问http://my.58.com/ 的话，登录就失败了。

经测试，只需要www58com，58cooper，PPU 三个cookie就行了。

附一张登录成功后的截图和代码，代码里面的http请求类和md5类是我自己用顺手的，按流程替换成自己的就行了。


![alt text](/assets/img/58success.png "success")


{% highlight csharp %}

    public static CookieContainer Login58(string name, string pass)
    {
            IHttpForm http = HttpFormFactory.DefaultHttpForm();

            HttpFormGetRequest request = new HttpFormGetRequest();
            request.Url = "https://passport.58.com/login/";

            HttpFormResponse response = http.Get(request);

            Match m = Regex.Match(response.Response, "<input\\stype=\"hidden\"\\sname=\"path\"\\svalue=\"(?<path>[^\"]+)\"/>", RegexOptions.Multiline | RegexOptions.IgnoreCase);

            string path = HttpUtility.UrlEncode(m.Groups["path"].Value);
            string p1;
            string p2;

            m = Regex.Match(response.Response, "var\\stimespan\\s=\\s(?<timespan>\\d+)\\s-\\snew", RegexOptions.Multiline | RegexOptions.IgnoreCase);

            double timespan = double.Parse(m.Groups["timespan"].Value) - MiniNet.Utility.DateHelper.GetTimestamp() * 1000;

            // var timesign = new Date().getTime() + timespan;
            double timesign = MiniNet.Utility.DateHelper.GetTimestamp() * 1000 + timespan;

            //hex_md5(hex_md5(c) + a.substring(5, 11));
            p1 = MD5.MDString(MD5.MDString(pass) + timesign.ToString().Substring(5, 6));

            //hex_md5(hex_md5_16(c) + a.substring(5, 11));
            p2 = MD5.MDString(Reverse(MD5.MDString(pass).Substring(8, 16)) + timesign.ToString().Substring(5, 6));

            m = Regex.Match(response.Response, "<input\\stype=\"hidden\"\\sid=\"ptk\"\\sname=\"ptk\"\\sid=\"ptk\"\\svalue=\"(?<ptk>[^\"]+)\"/>", RegexOptions.IgnoreCase | RegexOptions.Multiline);

            string ptk = m.Groups["ptk"].Value;

            m = Regex.Match(response.Response, "<input\\stype=\"hidden\"\\sid=\"cd\"\\sname=\"cd\"\\sid=\"cd\"\\svalue=\"(?<cd>[^\"]+)\"/>", RegexOptions.IgnoreCase | RegexOptions.Multiline);

            string cd = m.Groups["cd"].Value;

            string username = HttpUtility.UrlEncode(name);

            string data = string.Format("isweak=0&path={0}&p1={1}&p2={2}&timesign={3}&ptk={4}&cd={5}&username={6}&password=password&remember=on", path, p1, p2, timesign, ptk, cd, username);

            string postUrl = "https://passport.58.com/dounionlogin";

            HttpFormPostRawRequest postRequest = new HttpFormPostRawRequest();
            postRequest.Url = postUrl;
            postRequest.Referer = "https://passport.58.com/login/";
            postRequest.Data = data;
            postRequest.Cookies = new CookieContainer();

            response = http.Post(postRequest);

            if (response.Response.Contains("验证码"))
            {
                request.Cookies = response.Cookies;
                request.Url = "https://passport.58.com/validatecode";
                response = http.Get(request);

                File.WriteAllBytes("d://58.jpg", response.Bytes);

                timesign = MiniNet.Utility.DateHelper.GetTimestamp() * 1000 + timespan;

                //hex_md5(hex_md5(c) + a.substring(5, 11));
                p1 = MD5.MDString(MD5.MDString(pass) + timesign.ToString().Substring(5, 6));

                //hex_md5(hex_md5_16(c) + a.substring(5, 11));
                p2 = MD5.MDString(Reverse(MD5.MDString(pass).Substring(8, 16)) + timesign.ToString().Substring(5, 6));

                string validatecode = "xrney";

                data = string.Format("isweak=0&path={0}&p1={1}&p2={2}&timesign={3}&ptk={4}&cd={5}&username={6}&password=password&validatecode={7}&remember=on", path, p1, p2, timesign, ptk, cd, username, validatecode);

                postRequest.Data = data;
                postRequest.Cookies = response.Cookies;

                response = http.Post(postRequest);
            }

            m = Regex.Match(response.Response, "(?<num>\\d+)\"", RegexOptions.Multiline | RegexOptions.IgnoreCase);

            string num = m.Groups["num"].Value;

            CookieContainer cc = new CookieContainer();

            string cookies = response.Headers["Set-Cookie"];

            cookies = cookies.Replace("Path=/,", "");

            string[] array = cookies.Split(';');

            string www58com = array[0].Substring(9);
            string c58cooper = array[5].Substring(10);
            string c58passport = array[13].Substring(12);
            string ppu = array[16].Substring(5);

            cc.Add(new Cookie("www58com", www58com, "/", "58.com"));
            cc.Add(new Cookie("58cooper", c58cooper, "/", "58.com"));
            //cc.Add(new Cookie("58passport", c58passport, "/", "58.com"));
            cc.Add(new Cookie("PPU", ppu, "/", "58.com"));

            //request.Url = "https://passport.58.com/track/ppt/referrer2.js";

            //response = http.Get(request);

            //cookies = response.Headers["Set-Cookie"];

            //cc.Add(new Cookie("id58", cookies.Split(';')[0].Substring(5), "/", "58.com"));

            request.Url = "http://my.58.com/?pts=" + num;
            request.Referer = "http://passport.58.com/dounionlogin";
            request.Cookies = cc;

            response = http.Get(request);

            return response.Cookies;
        }

{% endhighlight %}

