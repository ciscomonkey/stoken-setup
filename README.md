# stoken setup

I use RSA SecurID for everything, but have had consistent issues with the native Mac SecurID client.  I decided to switch to [stoken](https://github.com/cernekee/stoken) and this is my setup.

#### Note on multiple RSA tokens

I have 2 different RSA tokens that I use depending on the functions I'm performing at the time.  To use multiple tokens with stoken you simply need to use multiple rc files.  For my setup, I use ```~/.stokenrc-[function]``` to name them, then use the ```--rcfile [filename]``` parameter.  I leave the "default" token (the one I use the most) configured in ```~/.stokenrc```.

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
Enter new password: [Enter new password]
Confirm new password: [Enter new password]
```

I know security can get annoying for users, but for the sake of your security **DO NOT LEAVE YOUR NEW PASSWORD BLANK**. If nothing else, set it to your PIN. If you leave it blank, and someone gets your ```~/.stokenrc``` file all they need to do is figure out your PIN and they can use it themselves.

**Note:** If you are using multiple tokens, this is where you should copy ```~/.stokenrc``` to ```~/.stokenrc-[function/org]```.

### Add your password to keychain

This is only if you required a password to decrypt the token during import.  This typically means that you are not prompted for a PIN to retrieve your token.  

1. Open the keychain.app
1. Create a new entry by pressing the + button on the bottom of the window.
1. Set Keychain Item Name to "rsapassword"
   1. If using multiple tokens that require PIN, then you should use an identifier for it such as "rsapassword-[function]" or "rsapassword-[org]" etc.  You will need to set this in the variables of the Applescript below.
1. Set Account Name to your AD User ID
1. Set the password to the new password you used during the token import in the previous step.
1. Click Add

### Add your PIN to keychain

1. Open the keychain.app
1. Create a new entry by pressing the + button on the bottom of the window.
1. Set Keychain Item Name to "rsatoken"
   1. If using multiple tokens that require PIN, then you should use an identifier for it such as "rsatoken-[function]" or "rsatoken-[org]" etc.  You will need to set this in the variables of the Applescript below.
1. Set Account Name to your AD User ID
1. Set the Password to your RSA PIN
1. Click Add

### Test and authorize security to access keychain

Open a terminal and type the following:

```shell
$ security -q find-generic-password -gl rsatoken
```

Substitute ```rsatoken``` with whatever name you used in the previous step if you have multiple RSA PINs.  You will use ```rsapassword``` if you are using passwords instead of PIN to access your token:

```shell
$ security -q find-generic-password -gl rsapassword
```

If prompted for your password, make sure to click the **Always Allow** button.  If you didn't, just run the command again.

### Install the script

1. Open the appropriate script based on if you are required to enter a pin or password.  If you read the instructions above, you should understand why you should not be using the no pin or password script.
1. Edit the ```rcFile``` variable if required.
   1. For mine, I have ```~/.stokenrc-func1``` and ```~/.stokenrc-func2``` that I use.
1. Edit the ```keychainPIN``` or ```keychainPW``` variable if required.
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

