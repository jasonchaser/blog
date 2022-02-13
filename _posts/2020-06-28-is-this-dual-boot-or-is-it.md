---
title: This is not Dual Boot… or is it?
description: "The soft thud-thud of the running boots..."
author: jasonchaser
image: https://source.unsplash.com/featured?droid
tags:
  - technology
  - linux
  - booting
---

> The soft thud-thud of the running boots was gradually drowned by the loud thumping from within. “Hey Rick! Eight O’clock!”. Bo…om! Desert Eagle. ‘You never know who is out’ I rasped gulping in the frosty air. With asynchronous cover fire we skid down to the cliff edge. Peering through the telescopic sight of Barrett MRAD I gazed the wasteland yet to conquer. But suddenly out of nowhere I heard the unmistakable click of the Walther P22 - cold, crisp and clear. Before a second thought flashed…, “GAME OVER” glowed ominously across the screen.

In this never ending world of electronic junk, new software springs up faster than the speed of cell growth in a human body. You’re probably reading this on an Android or Windows or Mac or even Linux device. Anyway for you Android gamer folks out there, you may have come to some point of time where you felt the screen size just seemed too small. If you could just stretch the screen a bit more… but then you find that it’s never enough!

You might have never heard of Android on PC but yeah it’s as real as the pillow you slept on last night!

What you might have tried…

1. Gaming paraphernalia paired with phones or tablets. They are nice and neat but hefty.
2. Virtual Boxes… ugh! (no offence Oracle) It’s indeed good for testing. But they are usually resource hogs and exceptionally good at getting stuck. For those who have used emulators, you know how slow your system becomes, it’s difficult even to move the cursor if you are on a low-end device.
3. Screen Mirroring… hmm? Clever. Applications like scrcpy and vysor are catching up but still a long way.

The above three options may fail due to issues like unavailable support, less memory or CPU power, driver incompatibly etc.

> What if you wanted to do something to that old PC in your attic? How could you turn something that old…, somehow into a productive android PC?

---

## Dual Boot?

Some of you folks know the hell of trouble you’ve been through while partitioning. Sometimes it simply ridiculous because your MBR (not GPT) won’t support more than 4 primary partitions and you’ve BIOS instead of UEFI. Duh! On top of it you’ve to mediate the eternal fight for boot-loader priority between Windows and other operating systems. Dual booting can be a fantastic mess… just like your first omelette!

> Hold on… amidst this craziness you’re asking me to boot into Android too…?!

### How about PrimeOS?

PrimeOS is an Android OS which can run on x86 CPUs. Usually mobile operating systems run on ARM CPUs. It takes an incredible effort on the part of developers to shift from one CPU architecture to another. The open-source [Android x86 project](https://www.android-x86.org/) is an example of one such gargantuan task. Interested? Read more on the Android CPU’s architectural differences on [Android Authority](https://www.androidauthority.com/arm-vs-x86-key-differences-explained-568718/).

<figure class="full-bleed" markdown="1">

![PrimeOS Lock Screen](https://miro.medium.com/max/700/1*n4E5ROmBjNTlUgLhyR3B5Q.jpeg)

<figcaption>PrimeOS lock-screen on Dell System XPS L502X</figcaption>
</figure>

### On Windows?

Head on to <https://primeos.in/download/>

- If you have a new PC (whose make is 2014+) download the mainline installer.
  **Other requirements**: Intel Core i series 5th gen or above OR Intel celeron N4000 OR AMD A8 7410 etc.
- If you have a PC (whose make is 2011+) download the standard installer.
  **Other requirements**: Intel Core i series 2nd gen or above OR Intel celeron N3350 OR AMD A8 5550M etc.
- If you have an older PC download the classical installer.
  **Other requirements**: Intel Core i series 1st gen or below OR Intel Pentium dual core OR Intel Pentium G620 OR AMD Phenom II X6 1100T etc.

Download the .exe file on all the above cases. The primary object of concern is support for an instruction set called [SSE 4.2](https://en.wikipedia.org/wiki/SSE4#SSE4.2). You can get CPU info in multiple ways: `msinfo32.exe`, `wmic cpu list /format:list` etc. Follow the instructions and you’ll be on the go.

### On Linux?

> Note: This tutorial follows Ubuntu based distributions (I’m on [elementary os](https://elementary.io/)). If you have any other Linux distributions like Fedora, Gentoo or Arch, you can definitely install PrimeOS the same way but creating a custom boot script will slightly vary.

<figure class="constrained" markdown="1">

![CPU Info](https://miro.medium.com/max/700/1*HsSGIPopUQAjKI5UkltGWQ.png)

<figcaption>lscpu</figcaption>
</figure>

Navigate to <https://www.primeos.in/download/> and download the most appropriate .iso file to your system. The same three constrains apply here too. To find CPU info run `lscpu` in the terminal. Under the Flags section if you see `sse4_2` implies, your CPU supports SSE 4.2 instructions.

Accordingly (also considering the other CPU requirements) you can choose which .iso file to be downloaded, mainline / standard / classic.

#### Preparing Bootable Space…

- Create a folder named primeos in your home directory.
- Extract the downloaded .iso contents. Copy initrd.img, install.img, kernel, ramdisk.img and system.sfs to the primeos directory.
- Create an empty directory called data in primeos folder. This is where all your android data be stored.

<figure class="third" markdown="1">

![home_folder](https://miro.medium.com/max/1038/1*T0o8niT1ovzqiNE76_1fxA.jpeg)
![extracted_folder](https://miro.medium.com/max/1038/1*TdLSFJkuV5jJmE7t9tLHOQ.jpeg)
![prime_os_folder](https://miro.medium.com/max/1038/1*q8rxINwLdNXaQ5I8mTCH0w.jpeg)

<figcaption><p><strong>Left:</strong> PrimeOS folder | <strong>Center:</strong> Extracted ISO | <strong>Right:</strong> Copied Contents</p></figcaption>
</figure>

If you haven’t already installed GRUB customizer you can do it by running:

```sh
# grub customizer
$ sudo apt update
$ sudo apt install software-properties-common
$ sudo add-apt-repository ppa:danielrichter2007/grub-customizer
$ sudo apt install grub-customizer
```

<figure class="constrained" markdown="1">

![Testing PrimeOS on Virtual Box](https://miro.medium.com/max/700/1*3VejPiEbtEOYkz0aiRGJ-A.jpeg)

<figcaption>'Testing' PrimeOS on VirtualBox</figcaption>
</figure>

#### Setting up a custom boot menu…

<figure class="constrained" markdown="1">

![Custom Boot Script](https://miro.medium.com/max/500/1*0T84OKII6I68ZbgHaB1xmw.png)

<figcaption>Custom Boot Script</figcaption>
</figure>

Open up the grub-customizer and create a new boot entry. Name it as `PrimeOS` with the following contents:

```text
insmod part_gpt
search --file --no-floppy --set=root /home/joe/primeos/system.sfs
linux /home/joe/primeos/kernel root=/dev/ram0 androidboot.selinux=permissive buildvariant=userdebug SRC=/home/joe/primeos
initrd /home/joe/primeos/initrd.img
```

Note: The line numbers (1, 2, 3 etc.) are NOT required. They just show the contents within a single line refer the screenshot). Replace `joe` with your `username`.

Click <kbd>OK</kbd>. Switch to `General settings` tab and check `show menu` option. Hit `Save` and exit the grub customizer.

<figure class="constrained" markdown="1">

![General Settings](https://miro.medium.com/max/700/1*mitmEpVhHPHBeP2888GZKQ.png)

<figcaption>General Settings in GRUB Customizer</figcaption>
</figure>

Now reboot the system and your GRUB menu should contain the PrimeOS entry. Here’s what mine looks like:

<figure class="constrained" markdown="1">

![GRUB](https://miro.medium.com/max/700/1*K7fjj5nBMUo3kJmyiNqqfg.jpeg)

<figcaption>PrimeOS : Last entry in the GRUB Menu</figcaption>
</figure>

**Some PrimeOS Screenshots:**

<figure class="third" markdown="1">

![home_folder](https://miro.medium.com/max/1280/1*XBoLMJLimW1wmrT1HKsw8Q.jpeg)
![extracted_folder](https://miro.medium.com/max/1280/1*zFDjJzPsd-95g5RVf-Ch4w.jpeg)

<figcaption><p><strong>Left:</strong> Booting PrimeOS splash | <strong>Right:</strong> Desktop with multiple windows</p></figcaption>
</figure>

#### Known Issues

While testing in Dell System XPS L502X, everything worked fine - speakers, microphone, WiFi etc. It comes to lock screen automatically sometimes.
But testing on another PC I found that USB WiFi with only standard network cards work. The microphone was not working, haven’t tried the workaround yet which looks promising.

## Conclusion

Selecting the version of PrimeOS will take time. Make sure you have all the specified requirements and you download the most appropriate image.

This method of booting and operating system is awesome. No virtual environment, no messing around with partitions, make full use of your RAM and CPU, navigate effortlessly with keyboard and mouse across multiple windows and that too on a relatively enormous screen! In case you didn’t like it (chances are slim)… all you need to do is delete the `primeos` folder and the custom boot entry as well.

All your data is stored at `/home/$USER/primeos/data`. You can access it with admin privilege. This provides a great way to transfer data to and fro with PrimeOS. I’ve also tried alternatives like Android-x86, PhoenixOS, RemixOS (discontinued) but by far the most comfortable one has been PrimeOS. I’m also trying to get the PrimeOS icon on the boot menu. If anyone knows how to add it to the custom boot script, please help me [here](https://unix.stackexchange.com/questions/593562/how-to-add-icon-to-a-custom-grub-boots-script).

### Reference(s)

<sup>\*</sup>[Fosslicious : How to install PrimeOS on Ubuntu](https://www.fosslicious.com/2018/12/how-to-install-primeos-in-ubuntu.html)<br/>
<sup>\*\*</sup>[XDA: Variant of PrimeOS distribution](https://forum.xda-developers.com/showpost.php?p=78858613&postcount=2387)

> Originally published on [Medium](https://joe733.medium.com/this-is-not-dual-boot-or-is-it-4c8c8f1a453a).
