# CIQ Development With VSCode - The Gotchas


## The Connect IQ Eclipse Plugin is dead! Long live the Connect IQ Eclipse Plugin!
It has been about 5 months since Garmin held their [annual developer conference](https://forums.garmin.com/developer/garmin-developer-virtual-conference-2020/),
during which there was an entire breakout session for Connect IQ tools, including the all singing,
all dancing, [Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=garmin.monkey-c).
Drinking the Kool-Aid, one would have thought Garmin had lowered the barrier to entry
for development on the CIQ platform, in their own words

{{< admonition type=quote open=true >}}
When Connect IQ launched, we provided a plugin for Eclipse as our development environment.
The Eclipse IDE is sophisticated at best, and complicated at worst, to the point that
Eclipse can be a barrier to entry for learning Connect IQ.
{{< /admonition >}}

The Eclipse environment isn't great, the IDE feels outdated, it lacks extensibility
and even the simplest tasks such as changing the default theme has you jumping
through multiple hoops. Frankly, it was such a bad experience that I would only use Eclipse to edit my `manifest.xml` file.

I actually went as far as adding a couple of functions to my ZSH
profile so that I could avoid opening Eclipse for most everyday
development.

{{< admonition type=note title="ZSH profile commands for the curious" open=false >}}
```bash
###
# Garmin Command Line Simulator
###

ciq () {
 echo "Starting ConnectIQ Simulator"
 command ~/ConnectIQ/connectIQ

 echo "Building Project: ${1}.prg \nDevice Type: ${2}"
 command ~/ConnectIQ/monkeyc -o bin/"${1}".prg -f monkey.jungle -y /key/garmin_developer_key -d "${2}"

 echo "Starting ${1}.prg on ${2}"
 command ~/ConnectIQ/monkeydo bin/"${1}".prg "${2}"
}

buildciq () {
  echo "Starting Build of $1"
  command ~/ConnectIQ/monkeyc --release --package-app --private-key /key/garmin_developer_key -f monkey.jungle -o IQ/$1.iq

  echo "Build finished"
  command ls -lah IQ/$1.iq
}
```
{{< /admonition >}}

Despite these shortcomings, when it came to setting up Eclipse on your machine, Garmin
provided you with a complete set of steps covering how to install and configure your
development environment.

In fact, at time of writing, taking at look at Garmin's
[getting started](https://developer.garmin.com/connect-iq/connect-iq-basics/getting-started/)
and [SDK](https://developer.garmin.com/connect-iq/sdk/) documentation, Eclipse is still the chosen / supported development
environment.

Now, it is true that feature parity between the  plugin for Visual Studio Code and
Eclipse is not quite there, which would explain the reason for downplaying the VS Code
side of CIQ development. However, were you to stumble across one of the few buried
posts on [Garmin Forums](https://forums.garmin.com/) or the
plugin's [marketplace page](https://marketplace.visualstudio.com/items?itemName=garmin.monkey-c)
you'd be forgiven for thinking you only need to follow the following 5 steps to get up and running

{{< admonition type=quote open=true >}}
1. Install extension
2. Download the Connect IQ SDK Manager
3. Download an SDK and set it as the current SDK using the SDK Manager
4. Download devices using the SDK Manager
5. Run the Monkey C: Verify Installation command to ensure the development environment is correctly setup
{{< /admonition >}}

That, I'm afraid, is not entirely accurate. There are a few gotchas I've come across
while getting set up with the plugin and I thought it would be worthwhile mentioning
them to save others the troubles I went through.

##  Incorrect documentation is often worse than no documentation.

### **Gotcha 1:** Environment
There is a prerequisite of the Oracle Java Runtime Environment (version 1.8.0 or higher),
which applies to any Connect IQ development you want to do. You'll hit this
roadblock pretty quickly if you tried to run any projects after setting up your plugin.
If I was being completely honest, this gotcha isn't unique
to the Visual Studio Code plugin, but then this goes back to my
original point that when a user is setting up Eclipse you're told
of this requirement, kind of...

You see, this little nugget of information is present within the getting started
guide on the Garmin Developer website, but not readily surfaced during SDK or
plugin download 🤷‍♂️

Anyways, onto the solution. For those on MacOS, assuming you use the [Homebrew](https://brew.sh)
package manager, you can install the Runtime Environment with the following:

```
brew install oracle-jdk
```

### **Gotcha 2:** Visual Studio Code Workspace Layout Matters
This gem was a joy to unpick...it turns out, that when you try to use the Monkey C plugin
from within Visual Studio Code the layout of your workspace will determine if your project runs
or not.

This may not matter to everyone, but I'd be surprised if I'm the only one who has fallen foul of
this bug(?). I like to keep my things organised, so my code layout is something like this:

```
garmin
├── faces
│   ├── face1
│   └── face2
├── fonts
│   └── font1
└── widgets
    └── widget1
```

If I'm working on watch faces, I'll usually open up a workspace in VS Code, which in turn loads my
`garmin/faces/*` directories, the workspace is seen as a single parent folder in VS Code
with all project folders available to me in that single tree view.

Take the below screenshots, the first shows a workspace which is made up of a single folder,
containing all other folders. This is how I usually use VS Code.

{{< image src="/posts/images/2021/03/20/single-parent-workspace.png" width="33%" height="33%" >}}

The following screenshot, shows a workspace that is made up of
multiple folders.

{{< image src="/posts/images/2021/03/20/multi-folder-workspace.png" width="33%" height="33%" >}}

The net result is largely the same, a view of my watch faces available from one sidebar,
crucially however, only the setup in the second screenshot will allow you to run/build
any of your applications.

If you try to launch an application from the single folder workspace

{{< image src="/posts/images/2021/03/20/single-parent-run.png" width="33%" height="33%" >}}

You end up with the following error

{{< image src="/posts/images/2021/03/20/monkey-c-error.png" width="33%" height="33%" >}}

Same project, same code, but different workspace layout

{{< image src="/posts/images/2021/03/20/multi-folder-run.png" width="33%" height="33%" >}}

You get a running application

{{< image src="/posts/images/2021/03/20/running-app.png" width="33%" height="33%" >}}

I've lost track of how many hours I spent trying to work this one out or where the issue
lies.
I cannot say who is to blame for it, Garmin, Microsoft or my perverse use of workspaces.
Hopefully, I've saved someone else the hassle with this...

### **Gotcha 3:** Manifest Editing Became That Little Bit Harder
The `manifest.xml` file is used to set metadata components of your application, typically
things like app id, the app type, targeted devices, supported languages and a few
other bits. You know, the bits of information that are only of any importance if you
want to test or release your application.

If you really wanted to, you could
[create and populate](https://developer.garmin.com/connect-iq/core-topics/manifest-and-permissions/)
this file by hand, though I don't think this is advised.

```
<!-- This is a generated file. It is highly recommended that you DO NOT edit this file. -->
```

That comment populates the head of all manifest files generated during project creation.
I'm guessing it's pretty important.

Luckily for us, the Visual Studio Code plugin offers the ability to populate
the manifest file automatically. That is, once you've completed the song and dance
required to access the correct command from within the command palette.

The plugin has several options which can be triggered, the options and their roles are below

* `Monkey C: Edit Application` - to update the application attributes
* `Monkey C: Set Products by Version` - lets you add all products that meet the minimum
SDK version of the application
* `Monkey C: Edit Products` - lets you add or remove any product
* `Monkey C: Edit Permissions` - to update permissions
* `Monkey C: Edit Languages` - to edit your compatible language list
* `Monkey C: Configure Monkey` Barrel - to edit the included barrels

Once triggered, you go on to complete a form like user interface that updates the manifest
file as you work your way through it.

So what's the problem? Well, you see, while the above does work, and the UI/UX is
most likely the best that can be done within the constraints of a Visual Studio Code
plugin, it just isn't as streamlined as Eclipse...yes, I said it.
Eclipse actually offers a better experience for this task 🤯

The two areas which I think users will feel the impact of this new manifest editing
system are:

* **Ease of use** - in Eclipse you don't need to remember several different commands
in order to edit different aspects of your manifest file, you simply click on the file
itself and are taken to the manifest editor viewport.

  {{< image src="/posts/images/2021/03/20/manifest1.png" width="33%" height="33%" >}}

  It's not perfect, something could be said about the sheer amount whitespace and
  the small, almost invisible tabs at the bottom of the screen (which I've highlighted).
  At least everything is accessible in one place and I'm not going to forget which incantation
  of the command palette I need to edit a particular piece of the manifest file.

* **Missing information** - this is particularly pertinent when working with the ever
increasing Garmin product list. In Eclipse, you could hover over a product
and you'd get some basic information about its screen resolution and shape of a given product.

  This may seem to be irrelevant information, but when trying to support some 60+
  products having the ability to check if a product is compatible with your application's
  screen shape and resolution layouts is up there with knowing the memory constraints
  and SDK compatibility of each device.

  In the below screenshots you can see the Eclipse product list vs the Visual
  Studio Code product list alongside additional device data achieved by hovering over
  a product. Hint, only one screenshot shows this additional data, unsurprisingly, it's also the one I prefer!

  {{< image src="/posts/images/2021/03/20/manifest2.png" width="33%" height="33%" >}}

  {{< image src="/posts/images/2021/03/20/product-list.png" width="33%" height="33%" >}}

  Now in all honesty, if your desire is to really understand device compatibility
  [there's a web page](https://developer.garmin.com/connect-iq/compatible-devices/) that
  provides more information about the subject. But surely Garmin could fit a few extra characters
  somewhere in their Visual Studio Code dropdown?

All in all, the Visual Studio Code plugin is still in development, Garmin have been
releasing relatively frequently and I expect as Eclipse gets phased out in favour of
VS Code, some of the UI and UX issues I've mentioned will get fixed.

Given where I am in my journey developing for the CIQ platform, the VS Code extension
has meant I could ditch my ZSH profile functions, instead running and building my
application from VS Code directly. However, I've not yet uninstalled Eclipse, the
experience determining which devices I can or want to support for my applications is
simply that much better than the VS Code alternative, which is a frustrating mix of scrolling
between VS Code's product listing and through Garmin's device compatibility page.

And with that, thanks for reading.

