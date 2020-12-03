---
layout: post
title: "Artix installation and UEFI issue"
date: 2020-11-30 20:51:00 +0100
tags: linux artix uefi
author: falc0n9
---
Today I finally installed Artix Linux with OpenRC. There are three different options when you go with this systemd free distribution, *openrc, runit and s6*. I do not have experience in any of them so I basically picked the one that was better rated in my google searches. Hopefully within the next weeks I will have learnt enough to blog about experience.

While my previous installations of Arch went smoothly, this time I had some trouble with my SSD disk. After some unsuccessful attemps, I finally found a solution which is perfectly explained in this [video](https://www.youtube.com/watch?v=dOXYZ8hKdmc). Kudos for him!.

Things that worked for me.
1. The first partition should be at least 500Mb and is meant to be for EFI
2. The type he picked for this partition was regular Linux filesystem type
3. Do not mount the partition during the bootstrapping and configuration
4. Just after installing grub bootloader and without generating the grub config, we should:
	{% highlight bash %}
	# Create an efi folder within /boot
	mkdir /boot/efi
	# Mount your boot partition in that point
	mount /dev/sdX1 /boot/efi
	# Install grub. Pay attention to the target parameter (ends with -efi)
	grub-install --target=x86_64-efi --bootloader-id=GRUB --efi-directory=/boot/efi
	# Generate grub config as always
	grub-mkconfig -o /boot/grub/grub.cfg
	# Some hardware might cause issues. This is the workaround
	mkdir /boot/efi/EFI/BOOT
	# We copy the boot we just created in this directory and uppercase its name
	cp /boot/efi/EFI/GRUB/grubx64.efi /boot/efi/EFI/BOOT/BOOTX64.EFI
	# To be even safer, we create a startup script
	printf 'bcf boot add 1 fs0:\EFI\GRUB\grubx64.efi "My GRUB bootloader"\nexit' > /boot/efi/startup.nsh
	{% endhighlight %}

I hope this works for you too. If you have any issues, let me know and I will try to help you.
