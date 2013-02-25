# Xmonad Plus Ubuntu Jaunty Jackalope

Yesterday, after reading tombuntu.com’s article on Xmonad:

    What good is having a large display if you’re constantly rearranging windows to fit them on the screen?
    I got tired of try to fit a web browser with other smaller windows and decided to try xmonad,
    a tiling window manager that could do this for me.

I decided to give it a whirl. Having upgraded on release day to Ubuntu 9.04 “Jaunty Jackalope”, I was suffering a little from “early adopter-ism” in that I couldn’t find articles that showed me how to get it up and running.
The articles that I had amassed (See my references at the bottom of the post) were good enough to allow me to install Xmonad as a new session in GDM, I could login, spawn a terminal and get up and running.
sudo apt-get install xmonad
Easy enough. But after years of being spoiled by Ubuntu’s notifications, multimedia keys, WiFi config and just so much more that we take for granted, I could not go back to a plain X session with none of this jazz.

Initially I didn’t care, Xmonad is so immediatly useful that I was prepared to forgo Gnome’s shinys. However, it appears that Canonnical have deviated from the standard way of using X options that I couldn’t get any startup applications to work, no matter where I put the commands. I tried .xsession, .gnomerc, modifying the xmonad.desktop GDM shortcut - all kinds of nasty hacks to no avail.

Then I found this page in the wiki, of how to replace Metacity (The Gnome window manger) with Xmonad. This seemed like a good compromise. However, none of the advice on the page (including the parts seemingly written for Jaunty) worked.
I finally got Xmonad working with two simple, simple lines in the Gnome “Sessions” app (Now called startup applications).

The first comand kills metacity,

    killall metacity
The advice from the wiki suggests putting && xmonad at the end of this command, to launch Xmonad straight away,
but this never worked. I could launch xmonad later though, which gave me the idea of delaying Xmonad:

    sh -c “sleep 2 && xmonad”

This is a little one-liner shellscript that waits for 2s before launching Xmonad.
And - presto! - it works. A remarkably small amount of work for what was a whole evening’s research.
A few tweaks make it nicer to use, I’ll detail them here.

Here is my ~/xmonad.hs file:
```haskell
import XMonad
import XMonad.Hooks.ManageDocks
import XMonad.Hooks.EwmhDesktops
import qualified XMonad.StackSet as W

myManageHook :: [ManageHook]
myManageHook =
[ resource =? “Do” —> doIgnore,
resource =? “gnome-do” —> doIgnore
]
main = xmonad $ defaultConfig
{ manageHook = manageDocks <+> composeAll myManageHook
, logHook = ewmhDesktopsLogHook
, terminal = “gnome-terminal”
, modMask = mod1Mask
, layoutHook = ewmhDesktopsLayout $ avoidStruts
$ layoutHook defaultConfig
}
```

Don’t ask me why they think a comma at the start of a line looks readable - it’s their crazy standard not mine!
So what do we have here, the imports and commands about `ManageDocks` stop it messing with gnome-panel,
and I have added a few exclusions for gnome-do (If you don’t use Do, you’re wasting your life,
same goes if you can’t use Do because of an O/S limitation) so that Do doesn’t get stretched out to fill a tiled window.

I use `mod1mask` for my mod key, left alt seems nicely under-thumb. `gnome-terminal` is an easy choice for
gnome users, I add a little transparency to the mix (cpu based via `xcompmgr`).

There you have it, I hope you enjoy your new life, freed from having to minimize, maximize,
resize and all that other screen fudge that you used to wade through hundreds of times a day!


# References

Original tombuntu post
http://tombuntu.com/index.php/2009/03/17/introduction-to-the-xmonad-tiling-window-manager/

Xmonad wiki
http://haskell.org/haskellwiki/Xmonad/Using_xmonad_in_Gnome

Ubuntu forums, “Tiling Window Manager (Xmonad) within Gnome with Gnome Do”
http://ubuntuforums.org/showthread.php?t=975329
