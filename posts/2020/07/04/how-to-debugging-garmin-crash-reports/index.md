# How to Debug Garmin Crash Reports


## There's a problem. Fix it.

Writing software usually comes with an element of support, this could be due to a of a lack of understanding on the user's
part, maybe a feature is being used in a way it wasn't thought of during development, a user might want something specific,
the developer could have introduced a bug etc etc... Regardless of the type of support request, developers need to collect
the right information to be able to act appropriately.

{{< admonition type=quote open=true >}}
INSTALLED WATCH FACE WILL NOT LOAD ON WATCH.  WATCH FACE SHOWS IQ!  WATCH IS SYNCING AND SHOWS AS CONNECTED.
{{< /admonition >}}

{{< admonition type=quote open=true >}}
Hi after downloading I get the "IQ?" icon. Was wondering how to fix?
{{< /admonition >}}

{{< admonition type=quote open=true >}}
Hi, I have a Forerunner 245 Music and I am unable to set this watch face on the watch. Once I install it and go to the Apply Face setting on the watch, I see an "IQ!" in the slot where this face should be.
{{< /admonition >}}

{{< admonition type=quote open=true >}}
I am having trouble loading this onto my Fenix 6 pro
{{< /admonition >}}

{{< admonition type=quote open=true >}}
Weather?
{{< /admonition >}}

The above are all examples of emails or reviews I've received while creating and releasing applications for Garmin devices,
some are a little light on details, others barely qualify as sentences, all told this makes collecting the right information
rather...difficult

Today, I'm going to highlight a small tool Garmin have provided which should help developers resolve application crashes with
a lot less friction. The tool itself, known as Exception Reporting Tool or ERA, was actually released in the Connect IQ
3.1 beta back in April 2019, however I only became aware of it recently. I figured there might be others out there who
have yet to discover it, maybe this post proves useful for someone.

## Ok great, do you mind sending some more info?

Before we get to the ERA tool however, I think it's kind of interesting to see where we're coming from, if you're not interested
in a brief history lesson, head on over to [The future is here, the future is plain(ish) English](#the-future-is-here-the-future-is-plainish-english).

Prior to the introduction of the ERA tool, if you wanted to find the crash report from you Garmin application you had to
look for the `CIQ_LOG.YAML` file that was written or updated on the device filesystem, `/GARMIN/APPS/LOGS`. If your device was
running a CIQ version older than Connect IQ 3.0, you'd be looking for `CIQ_LOG.TXT`. This resulted in more than a few emails
that went something along the lines of

{{< admonition type=quote open=true >}}
If you also forward your crash logs I can take a look, the following file will contain what I need

* CIQ_LOG.YAML found within the /GARMIN/APPS/LOGS directory within the watch filesystem.
{{< /admonition >}}


## Thanks! Now to translate this output into something halfway useful...

Typically responses would include the file I was after, but the contents weren't exactly shining a spotlight on the issue
```
Error: Symbol Not Found Error
Details: "Could not find symbol '0080012d'"
Time: 2020-02-19T10:23:54Z
Part-Number: 123-B1234-00
Firmware-Version: '15.20'
Language-Code: eng
ConnectIQ-Version: 3.1.7
Store-Id: 8e967022-abc7-43fa-b9f6-3053d0a86561
Store-Version: 15
Filename: A2I82652
Appname: Horizon
Stack:
  - pc: 0x100010b2
  - pc: 0x100017ea
  - pc: 0x30001642
  - pc: 0x300015aa
  - pc: 0x100002b7
```

From the above, I knew which application was downloaded. I also had a vague idea which version the user was running.
```
Store-Id: 8e967022-abc7-43fa-b9f6-3053d0a86561
Store-Version: 15
Appname: Horizon
```
The problem here is that `Store-Version` is incremented on each release, so I knew this was the 15th release of the Horizon
application, but what actual version of the codebase that translated to would be impossible to know unless you were keeping
your semver versioning in line with or referencing store releases.

Moving on, I also had some information about the CIQ version on the user's watch, as well as their firmware, but not their
watch model...
```
Firmware-Version: '15.20'
ConnectIQ-Version: 3.1.7
```

And while I technically had the details of the crash as well as the type of error and a stack trace
```
Error: Symbol Not Found Error
Details: "Could not find symbol '0080012d'"
Stack:
  - pc: 0x100010b2
  - pc: 0x100017ea
  - pc: 0x30001642
  - pc: 0x300015aa
  - pc: 0x100002b7
```

The provided stack trace doesn't really point me to anything right away, you see the stack trace is presented in
a HEX format. In order to be remotely useful, this had to be converted to decimal format. Taking the first line of the
stack trace would result in a conversion from `0x100010b2` to `268439730`. Still about as clear as mud.

After conversion, you had to then find the _closest_ line number in your application's debug file, to point you in the direction
of where the error was occurring. That's right, the conversion wouldn't highlight the line in the code
where the error occurred, you had to use the information and find the error based on the closest line according to the your
debug file.

So, open up the `Horizon.prg.debug.xml`, start searching for `268439730`, find nothing, start removing digits from the end
of the string until a match is found, something similar to this:

```
<entry filename="/Users/pavdjian/gitspace/home/garmin/faces/Horizon/source/info.mc" id="5" lineNum="388" pc="268439772" symbol="draw"/>
```

And it's at this point that you've finally got any idea as to what might be going wrong. Unsurprisingly, having to do this
this for multiple reports and apps is a relatively painful process.

## The future is here, the future is plain(ish) English

Enter ERA!
The tool itself can be launched either from the command line

`era [-a <arg>] [-k <arg>]`

Where `-a` is your AppID and `-k` is the key file you used to create the app.

Alternatively (and the way I use the tool), you could launch it from the Eclipse menu

`Connect IQ -> Start ERA`

On first start up you'll need to add your developer identity which is a case of entering your developer ID and developer key.

{{< admonition type=tip open=true >}}
To obtain your developer ID search for your apps in the app store and click on your developer name. The URL for this page will contain your developer ID. For example, if the URL is `https://apps.garmin.com/en-US/developer/5bfb3073-ce07-4509-bcae-771c5bef197e/apps` then the developer ID is `5bfb3073-ce07-4509-bcae-771c5bef197e`.
{{< /admonition >}}

Once you've done that, you should find you have access to all the apps you've released via a dropdown menu

{{< image src="/posts/images/2020/07/04/era_apps.png" >}}

Clicking on one of them, will display any and all crash reports associated with the app
{{< image src="/posts/images/2020/07/04/era_crash1.png" >}}

{{< image src="/posts/images/2020/07/04/era_crash2.png" >}}

Right away, it's clear that the reports are presented in a much better format for developer.
* For a start they're in plain(ish) English, you're provided with the line of code where the error is happening.
* On top of that, being an anonymised crash reporter you get information from all users rather than only those who reach
out to report a crash.
* Additionally, you get an occurrence count so that you're able to focus on resolving the most common crashes first.
* You're told the model of the watch which experienced the crash as well as the firmware version.
  * Interestingly, the CIQ version is omitted now though.
* Finally, you're given the real version of the app which is experiencing the crash rather than the `Store-Version`.

All in all, this has made debugging applications so much easier and it has made it possible to make this a proactive process
which results in a better experience for developers and users alike.

And with that, it looks like I have a few crash reports to solve 😅. Thanks for reading.

