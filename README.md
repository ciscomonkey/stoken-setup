# stoken setup

On my Mac, I use RSA SecurID for everything, but use stoken as I have had multiple issues with the SecurID Mac Client.

I have 2 different RSA tokens assigned to me for different functions.  To use multiple tokens, you simply need to create multiple ~/.stokenrc files. (Import token and copy the rc file to another name such as ~/.stokenrc-func1 and ~/.stokenrc-func2.)  You can leave the "default" token you use in the ~/.stokenrc file using this setup.

## Setup

### Install stoken

I installed ```stoken``` via Homebrew.

```shell
$ brew install stoken
```

I won't go over the multiple ways of importing tokens into stoken, but will say that in my environment it requires me requesting the STDID file for my token.  These instructions reflect my process.

### Import Token

```shell
$ stoken import --file filename.stdid
```

If you are required to use a password to decrypt the token it will look like this:

```shell
$ stoken import --file filename.stdid
Enter password to decrypt token: [Enter password provided by administrator]
Enter new password: [Press Enter]
Confirm new password: [Press Enter]
```

**Note:** If you are using multiple tokens, this is where you should copy ```~/.stokenrc``` to ```~/.stokenrc-[function/org]```.

### Add your PIN to keychain

1. Open the keychain.app
1. Create a new entry by pressing the + button on the bottom of the window.
1. Set Keychain Item Name to "rsatoken"
1. Set Account Name to your AD User ID
1. Set the Password to your RSA PIN
1. Click Add

### Test and authorize security to access keychain PIN

Open a terminal and type the following:

```shell
$ security -q find-generic-password -gl rsatoken
```

If prompted for your password, make sure to click the **Always Allow** button.  If you didn't, just run the command again.

### Install the script

1. Open the appropriate script based on if you are required to enter a pin or not.
1. Edit the rcFile variable if required
   1. For mine, I have ```~/.stokenrc-func1``` and ```~/.stokenrc-func2``` that I use.
1. Save the script to the ```~/Library/Scripts/``` folder.
   1. I name mine ```SecurID - [Func].scpt``` to describe which area the token is for.

### Add Scripts to the Menu Bar

1. Open the Script Editor
1. Click **Script Editor** > **Preferences**
1. On the General Tab, select **Show Script menu in menu bar**

## Using

Simply click on the Scripts Icon in the Menu bar and select the script you want to use.  You'll get a notification with the current code and it will be copied to the clipboard.

## TODO

* Add support for next pin option
