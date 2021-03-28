# How to Create Custom Card Decks With Tabletop Simulator


When Tabletop Simulator released in 2015, the idea of a single application that would
allow me to play board games no matter where I found myself was very appealing.
However, since it was a sandbox style game, unless you had others who were keen to
join in the fun there was little that you could do. About a year after its release, I
bought a four pack, shared the extra copies amongst friends and proceeded to play it a
grand total of 30 minutes. It has since sat in my Steam library untouched until this
week when I was looking for an application to test a prototype card game.

Due to various NDAs and verbal promises I can't divulge much about the card game
I wanted to prototype, however, I can show others how they could use Tabletop Simulator
to import card designs in order to prototype and play test their own card games.

## Prerequisites
Before we get started, you'll need:
* Some card designs. Obviously. I found some sample cards on [Reddit](https://www.reddit.com/r/tabletopgamedesign/comments/8wvw05/new_print_and_play_sheet_with_updated_artwork_for/)
* You'll also need a design for the card back, [Google helped me](https://cdn.shopify.com/s/files/1/0200/7616/products/0008_messymod-playing-cards_0008_back-design_1024x1024.png)
here.

## Berserk Games' Deck Builder

Undoubtedly the easiest and quickest method of getting your cards ready for use
in Tabletop Simulator would be the included [Deck Builder](https://kb.tabletopsimulator.com/custom-content/custom-deck/).

However, I've not been able to get this working with Mac OS. When I try to run the Deck Builder jar file
```
~/Library/Application\ Support/Steam/steamapps/common/Tabletop\ Simulator/Modding/Deck\ Builder/TSDB_v2.3.0.jar
```
I am greeted by an application window which seems to function, in that, I can create a
new deck and load the card designs correctly, but when I try export the images as a card sheet
it fails.

{{< image src="/posts/images/2021/03/28/deckbuilder.png" width="33%" height="33%" >}}

Fun fact, if you run the Deck Builder by double click the application file, the export fails silently.
Below you can see a screenshot of the Deck Builders log output which, in theory, should
give you an indication of what the application is doing and if any issues arise during use.

{{< image src="/posts/images/2021/03/28/logger.png" width="33%" height="33%" >}}

If I was to running the Deck Builder through the command line and try to export my card sheet
I get a stack trace of the export failure

```java
» java -jar TSDB_v2.3.0.jar
Exception in thread "AWT-EventQueue-0" java.lang.NoClassDefFoundError: javax/media/jai/Interpolation
	at com.anwinity.tsdb.ui.deckbuilder.DeckBuilderExportModal2$5.actionPerformed(DeckBuilderExportModal2.java:332)
	at java.desktop/javax.swing.AbstractButton.fireActionPerformed(AbstractButton.java:1972)
	at java.desktop/javax.swing.AbstractButton$Handler.actionPerformed(AbstractButton.java:2313)
	at java.desktop/javax.swing.DefaultButtonModel.fireActionPerformed(DefaultButtonModel.java:405)
	at java.desktop/javax.swing.DefaultButtonModel.setPressed(DefaultButtonModel.java:262)
	at java.desktop/javax.swing.plaf.basic.BasicButtonListener.mouseReleased(BasicButtonListener.java:279)
	at java.desktop/java.awt.Component.processMouseEvent(Component.java:6617)
	at java.desktop/javax.swing.JComponent.processMouseEvent(JComponent.java:3342)
	at java.desktop/java.awt.Component.processEvent(Component.java:6382)
	at java.desktop/java.awt.Container.processEvent(Container.java:2264)
	at java.desktop/java.awt.Component.dispatchEventImpl(Component.java:4993)
	at java.desktop/java.awt.Container.dispatchEventImpl(Container.java:2322)
	at java.desktop/java.awt.Component.dispatchEvent(Component.java:4825)
	at java.desktop/java.awt.LightweightDispatcher.retargetMouseEvent(Container.java:4934)
	at java.desktop/java.awt.LightweightDispatcher.processMouseEvent(Container.java:4563)
	at java.desktop/java.awt.LightweightDispatcher.dispatchEvent(Container.java:4504)
	at java.desktop/java.awt.Container.dispatchEventImpl(Container.java:2308)
	at java.desktop/java.awt.Window.dispatchEventImpl(Window.java:2773)
	at java.desktop/java.awt.Component.dispatchEvent(Component.java:4825)
	at java.desktop/java.awt.EventQueue.dispatchEventImpl(EventQueue.java:772)
	at java.desktop/java.awt.EventQueue$4.run(EventQueue.java:721)
	at java.desktop/java.awt.EventQueue$4.run(EventQueue.java:715)
	at java.base/java.security.AccessController.doPrivileged(AccessController.java:391)
	at java.base/java.security.ProtectionDomain$JavaSecurityAccessImpl.doIntersectionPrivilege(ProtectionDomain.java:85)
	at java.base/java.security.ProtectionDomain$JavaSecurityAccessImpl.doIntersectionPrivilege(ProtectionDomain.java:95)
	at java.desktop/java.awt.EventQueue$5.run(EventQueue.java:745)
	at java.desktop/java.awt.EventQueue$5.run(EventQueue.java:743)
	at java.base/java.security.AccessController.doPrivileged(AccessController.java:391)
	at java.base/java.security.ProtectionDomain$JavaSecurityAccessImpl.doIntersectionPrivilege(ProtectionDomain.java:85)
	at java.desktop/java.awt.EventQueue.dispatchEvent(EventQueue.java:742)
	at java.desktop/java.awt.EventDispatchThread.pumpOneEventForFilters(EventDispatchThread.java:203)
	at java.desktop/java.awt.EventDispatchThread.pumpEventsForFilter(EventDispatchThread.java:124)
	at java.desktop/java.awt.EventDispatchThread.pumpEventsForFilter(EventDispatchThread.java:117)
	at java.desktop/java.awt.WaitDispatchSupport$2.run(WaitDispatchSupport.java:190)
	at java.desktop/java.awt.WaitDispatchSupport$4.run(WaitDispatchSupport.java:235)
	at java.desktop/java.awt.WaitDispatchSupport$4.run(WaitDispatchSupport.java:233)
	at java.base/java.security.AccessController.doPrivileged(AccessController.java:312)
	at java.desktop/java.awt.WaitDispatchSupport.enter(WaitDispatchSupport.java:233)
	at java.desktop/java.awt.Dialog.show(Dialog.java:1079)
	at java.desktop/java.awt.Component.show(Component.java:1720)
	at java.desktop/java.awt.Component.setVisible(Component.java:1667)
	at java.desktop/java.awt.Window.setVisible(Window.java:1032)
	at java.desktop/java.awt.Dialog.setVisible(Dialog.java:1014)
	at com.anwinity.tsdb.ui.deckbuilder.DeckBuilderExportModal2.showModal(DeckBuilderExportModal2.java:389)
	at com.anwinity.tsdb.ui.deckbuilder.menu.file.ExportDeckMenuItem$1.actionPerformed(ExportDeckMenuItem.java:27)
	at java.desktop/javax.swing.AbstractButton.fireActionPerformed(AbstractButton.java:1972)
	at java.desktop/javax.swing.AbstractButton$Handler.actionPerformed(AbstractButton.java:2313)
	at java.desktop/javax.swing.DefaultButtonModel.fireActionPerformed(DefaultButtonModel.java:405)
	at java.desktop/javax.swing.DefaultButtonModel.setPressed(DefaultButtonModel.java:262)
	at java.desktop/javax.swing.AbstractButton.doClick(AbstractButton.java:374)
	at java.desktop/javax.swing.plaf.basic.BasicMenuItemUI.doClick(BasicMenuItemUI.java:1022)
	at java.desktop/com.apple.laf.AquaMenuItemUI.doClick(AquaMenuItemUI.java:157)
	at java.desktop/javax.swing.plaf.basic.BasicMenuItemUI$Handler.mouseReleased(BasicMenuItemUI.java:1066)
	at java.desktop/java.awt.AWTEventMulticaster.mouseReleased(AWTEventMulticaster.java:297)
	at java.desktop/java.awt.Component.processMouseEvent(Component.java:6617)
	at java.desktop/javax.swing.JComponent.processMouseEvent(JComponent.java:3342)
	at java.desktop/java.awt.Component.processEvent(Component.java:6382)
	at java.desktop/java.awt.Container.processEvent(Container.java:2264)
	at java.desktop/java.awt.Component.dispatchEventImpl(Component.java:4993)
	at java.desktop/java.awt.Container.dispatchEventImpl(Container.java:2322)
	at java.desktop/java.awt.Component.dispatchEvent(Component.java:4825)
	at java.desktop/java.awt.LightweightDispatcher.retargetMouseEvent(Container.java:4934)
	at java.desktop/java.awt.LightweightDispatcher.processMouseEvent(Container.java:4563)
	at java.desktop/java.awt.LightweightDispatcher.dispatchEvent(Container.java:4504)
	at java.desktop/java.awt.Container.dispatchEventImpl(Container.java:2308)
	at java.desktop/java.awt.Window.dispatchEventImpl(Window.java:2773)
	at java.desktop/java.awt.Component.dispatchEvent(Component.java:4825)
	at java.desktop/java.awt.EventQueue.dispatchEventImpl(EventQueue.java:772)
	at java.desktop/java.awt.EventQueue$4.run(EventQueue.java:721)
	at java.desktop/java.awt.EventQueue$4.run(EventQueue.java:715)
	at java.base/java.security.AccessController.doPrivileged(AccessController.java:391)
	at java.base/java.security.ProtectionDomain$JavaSecurityAccessImpl.doIntersectionPrivilege(ProtectionDomain.java:85)
	at java.base/java.security.ProtectionDomain$JavaSecurityAccessImpl.doIntersectionPrivilege(ProtectionDomain.java:95)
	at java.desktop/java.awt.EventQueue$5.run(EventQueue.java:745)
	at java.desktop/java.awt.EventQueue$5.run(EventQueue.java:743)
	at java.base/java.security.AccessController.doPrivileged(AccessController.java:391)
	at java.base/java.security.ProtectionDomain$JavaSecurityAccessImpl.doIntersectionPrivilege(ProtectionDomain.java:85)
	at java.desktop/java.awt.EventQueue.dispatchEvent(EventQueue.java:742)
	at java.desktop/java.awt.EventDispatchThread.pumpOneEventForFilters(EventDispatchThread.java:203)
	at java.desktop/java.awt.EventDispatchThread.pumpEventsForFilter(EventDispatchThread.java:124)
	at java.desktop/java.awt.EventDispatchThread.pumpEventsForHierarchy(EventDispatchThread.java:113)
	at java.desktop/java.awt.EventDispatchThread.pumpEvents(EventDispatchThread.java:109)
	at java.desktop/java.awt.EventDispatchThread.pumpEvents(EventDispatchThread.java:101)
	at java.desktop/java.awt.EventDispatchThread.run(EventDispatchThread.java:90)
Caused by: java.lang.ClassNotFoundException: javax.media.jai.Interpolation
	at java.base/java.net.URLClassLoader.findClass(URLClassLoader.java:433)
	at java.base/java.lang.ClassLoader.loadClass(ClassLoader.java:586)
	at java.base/java.lang.ClassLoader.loadClass(ClassLoader.java:519)
	... 84 more
```

So, assuming the Deck Builder application fails to export your cards, what other options are there?
1. You could try to create the card sheet manually. I'm guessing that would be quite painful.
2. You could find someone to do it for you, technically the sample cards I found for this post
were probably in a format that Tabletop Simulator could already consume.
3. You could find an alternative app or service to create the template.
4. You could figure out what is causing the Java app to fail and fix it.
5. You could just stick to physical prototypes...

Enter [Imagemagick](https://imagemagick.org/)!

I chose option 3, in the form of Imagemagick, which allows me to manipulate images
in a variety of ways, one of which, [`montage`](https://legacy.imagemagick.org/Usage/montage/),
should allow me to tile my images in a way that allows me to generate a card sheet.

{{< admonition type=quote open=true >}}
The "montage" command is designed to produce an array of thumbnail images. Sort of like a proof sheet of a large collection of images.
{{< /admonition >}}

Below I'm going to outline how you can use the command and hopefully remove some
of the guesswork I went through. I'm tailoring this for Mac OS, since it's what I
use, I would assume the process is the same elsewhere, but 🤷‍♂️

## Building A Custom Deck

1. Firstly, let's get Imagemagick installed.
    ```shell
    # Depending on the state of your machine, you might need a few dependencies
    # Grab a drink while this does its thing
    brew install imagemagick
    ```

2. Once installed, verify it's working by checking the `montage` version
    ```
    montage -version
    Version: ImageMagick 7.0.11-4 Q16 x86_64 2021-03-20 https://imagemagick.org
    Copyright: (C) 1999-2021 ImageMagick Studio LLC
    License: https://imagemagick.org/script/license.php
    Features: Cipher DPC HDRI Modules OpenMP(4.5)
    Delegates (built-in): bzlib freetype gslib heic jng jp2 jpeg lcms lqr ltdl lzma openexr png ps tiff webp xml zlib
    ```

3. Next, collect all the cards you want on your card sheet into a single directory.

    {{< admonition type=tip title="Names and Folder Structure" open=true >}}
Tabletop Simulator has a couple of restrictions on card sheet sizes, the smallest
they can be is `2x2` and the largest they can be is `10x7`. If you have more
than 70 cards, it would be ideal to split them into different folders or "decks".
A standard naming convention for your cards would be useful if you wish to keep
them in a single folder
    {{< /admonition >}}

    ```
    » tree
    .
    ├── actionDeck_dive.jpeg
    ├── actionDeck_dodge.jpeg
    ├── actionDeck_flee.jpeg
    ├── actionDeck_grapple.jpeg
    ├── actionDeck_jump.jpeg
    ├── actionDeck_kick.jpeg
    ├── actionDeck_punch.jpeg
    ├── actionDeck_sprint.jpeg
    ├── actionDeck_throw.jpeg
    ├── playerDeck_amateur.jpeg
    ├── playerDeck_bard.jpeg
    ├── playerDeck_duelist.jpeg
    ├── playerDeck_magician.jpeg
    ├── playerDeck_priest.jpeg
    ├── playerDeck_savage.jpeg
    ├── playerDeck_scrapper.jpeg
    ├── playerDeck_spy.jpeg
    └── playerDeck_thief.jpeg
    ```

4. With your cards collected, you want to pass the cards through to the `montage`
command

    ```shell
    montage \
    -geometry 440x636 \
    -tile 3x3 \
    playerDeck_amateur.jpeg playerDeck_bard.jpeg playerDeck_duelist.jpeg \
    playerDeck_magician.jpeg playerDeck_priest.jpeg playerDeck_savage.jpeg \
    playerDeck_scrapper.jpeg playerDeck_spy.jpeg playerDeck_thief.jpeg \
    cardsheet_playerDeck.jpg
    ```

    Let's break the command down:

    * `montage` - the type of transformation we want ImageMagick to do for us
    * `-geometry XxY` - the size in pixels of the cards you are using, so in my case
    440 pixels wide by 636 pixels tall
    * `-tile XxY` - the number of cards wide and number of cards tall the card sheet
    will be. Again, in my case, it's a 3 card wide, by 3 card tall card sheet
    * `playerDeck_*.jpeg` - the names of the cards I wish `montage` to use
    * `cardsheet_playerDeck.jpg` - the name of the output file I wish `montage` to create

    {{< admonition type=warning title="Potential Data Loss" open=true >}}
Make sure you **_always_**, include the final argument specifying an output file.
If you don't include one, ImageMagick will assume that the last card in your provided
card list is the file you wish to output the rendered card sheet to and it will overwrite
the file with no prompt or further user interaction.
    {{< /admonition >}}

5. At this point you should check that you have a card sheet that contains all
your card faces

    {{< image src="/posts/images/2021/03/28/cardsheet.jpg" width="33%" height="33%" >}}

6. Load up Tabletop Simulator and start a new game, you'll want to start with
an empty table, so feel free to exit out of all the menus and pop-ups that appear
when starting a new game

    {{< image src="/posts/images/2021/03/28/tts1.png" width="33%" height="33%" >}}

7. Create a "Custom Deck" object on the table, go through the menu
`Objects -> Components -> Custom -> Deck`, once you've got your object, drop it
anywhere on the table by clicking once.

    {{< image src="/posts/images/2021/03/28/tts2.png" width="33%" height="33%" >}}

8. Right click the newly created deck to open up the properties menu

    {{< image src="/posts/images/2021/03/28/tts3.png" width="33%" height="33%" >}}

9. Select the `cardsheet_playerDeck.jpg` we created as the **"Face"** of the card.
We can also set the card back design we downloaded earlier as our card **"Back"**.

    {{< admonition type=tip title="Local vs Cloud files" open=false >}}
When setting your designs, you will be prompted to load them locally or store
them in the cloud.

Choosing local will mean only you can see the designs and you can't use them in
multiplayer games.

The cloud option loads them into your personal cloud space, each user of Tabletop Simulator get 100GB of storage.

Alternatively, you can use a cloud storage solution of your choice to store the
pictures and load them via URL instead of local file reference
    {{< /admonition >}}

10. Set the **"Width"** and **"Height"** values equal to the values you used in
the `-tile` argument of your `montage` command. In our example earlier, 3x3.

11. Finally set the number of cards in your deck, again in our example 9 (3x3).

    {{< image src="/posts/images/2021/03/28/tts4.png" width="33%" height="33%" >}}

12. Click **"Import"**

    {{< image src="/posts/images/2021/03/28/tts5.png" width="33%" height="33%" >}}

13. Go have fun with all your lovely cards!

    {{< image src="/posts/images/2021/03/28/tts6.png" width="33%" height="33%" >}}

And with that, thanks for reading 🃏

