A few days ago, I decided to pull the trigger and install Windows on my Retina
MacBook Pro at a LAN Party. It's a perfectly fine machine with a decent
graphics card and CPU. I started Boot Camp Assistant and followed through with
the instructions to install from the USB drive using the ISO of Windows 7 I had
on my Desktop. *If you follow Apple's steps to the letter, your installation of
Windows will never proceed.*

If you do follow the steps, you will most certainly get an error of "Setup was
unable to create a new system partition or locate an existing system partition.
" when you select the BootCamp partition that you had just formatted in the
Windows installer on your Mac. This can occur on PCs too but basically the
issue is that the USB drive is initialized as another valid bootable disk. If
you were to Google this, you will find
[solutions](http://social.technet.microsoft.com/Forums/en-US/w7itproinstall/thread/9e18e169-f77e-4026-b22f-f602e670d55c/)
online that involve yanking out the USB drive and reinserting it before
pressing "Install Now" on the Windows 7 installation screen. This "Install Now"
screen is a big centered button that prompts your to install now and the arrow
is set inside a blue circle that's like a gem.

This is where Apple's helpfulness can get in your way. By default, Boot Camp
Assistant copies the Boot Camp support files to your USB drive. It also adds an
`Autounattend.xml` file to the root of your drive to automatically install Boot
Camp support drivers and utilities in Windows. With `Autounattend.xml` present,
you will not have the opportunity to yank out and reinsert the USB drive before
continuing. Instead, the setup just proceeds as if that button was pressed.
While this is a good file for disc installations, it causes a massive problem
for USB installation which is most likely to be the case for all new Macs from
  now on since they do not include disc drives by default.  If you try to ask
  Apple to 'fix' this, they will just blame Microsoft which admittedly isn't
  blame free either.

To solve this problem, move or delete Autounattend.xml on the root of the USB
drive and proceed with the normal steps afterwards .  After Windows is done
installing and since `Autounattend.xml` was nullified, you will have to run the
Boot Camp support installer from the BootCamp folder on the USB drive.

It's a shame Apple's instructions just do not work. The fix required would
probably mean removing `Autounattend.xml` functionality for USB Drive
installations. In the meantime, this worked for me.
