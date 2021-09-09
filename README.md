# stoken setup

I use RSA SecurID for everything, but have had consistent issues with the native Mac SecurID client.  I decided to switch to [stoken](https://github.com/cernekee/stoken) and this was an excercise to see if I could replace the missing gui on Mac.

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

I know security can get annoying for users, but for the sake of your security **DO NOT LEAVE YOUR NEW PASSWORD BLANK**. If nothing else, set a PIN style password - **DO NOT USE YOUR RSA PIN**. If you leave it blank, and someone gets your ```~/.stokenrc``` file they can use it themselves.

**Note:** If you are using multiple tokens, this is where you should copy ```~/.stokenrc``` to ```~/.stokenrc-[function/org]```.

### Scripts

The apple scripts are just examples of how to retrieve a PIN from your token.  It assumes you have set a password or PIN on your token (these should be completely different from any real username/password/pin and applied only for the token) and can retrieve this from keychain.  These should not be used to circumvent your security, they were written as an exercise to see if I could replace the missing gui feature on the RSA client and the stoken-gui which is not on Mac.

**BASICALLY** you shouldn't do anything below outside of a lab.

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

