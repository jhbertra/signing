# Key Management for Delegators and Voters

## Windows

### Step 1: Install necessary software

1. Open a Windows PowerShell command prompt
2. Install the `Scoop` package manager (https://scoop.sh)
   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
   Invoke-RestMethod -Uri https://get.scoop.sh | Invoke-Expression
   ```
3. Install `openssl` using `Scoop`:
   ```powershell
   scoop install openssl
   ```

### Step 2: Create an Ed25519 Private Key

1. Decide where you want to keep your private keys. You will need to remember
   where they are for future use. For example, you can keep them in a directory
   called `private-keys` in your user's home directory. We will proceed
   assuming this is what you want to do.

2. Open a Windows PowerShell command prompt.
   a. Aside on working directories: By default, PowerShell will open in your
      user's home directory. If you run the command `Get-Location` it will print out the
      absolute path of the working directory you are currently in. For example,
      here is what I see:
      ```powershell
      Get-Location

      Path
      ----
      C:\Users\Jamie
      ```
      The working directory is the directory PowerShell is currently
      "inside". You can change to a different working directory using the `Set-Location`
      command. For our purposes, we will remain in the default `~` (home) directory.
3. Create the directory. The command `New-Item` is used to make a directory. In
   PowerShell, run the command:
   ```powershell
   New-Item -Name "private-keys" -ItemType "directory"
   ```
   This command says to make a new directory called `private-keys` relative to
   the current directory. Recall that the current directory should be your home
   directory, so on my machine this would create the directory
   `C:\Users\Jamie\private-keys`.

4. Create your private key. From PowerShell, run the command:

   ```powershell
   openssl genpkey -aes256 -algorithm ed25519 -out private-keys\your-key-name-here.private
   ```

   With `your-key-name-here` replaced with some useful name, for example `cc-delegator` or `cc-voter`.
   Keep in mind that you may need to manage multiple keys in the future, so pick a naming scheme that will be easy to keep track of.
   For example, you could include the date in the name as well, e.g. `cc-delegator-2024-07-24` so you know which is the latest one.

   breaking this down:
   - `openssl genpkey` is a command used to generate private keys
   - `-aes256` says we want to encrypt the private key with the AES 256 cipher (widely considered to be one of the most secure symmetric ciphers)
   - `-algorithm ed25519` says to generate a key using the Ed25519 elliptic curve - this is the native algorithm for Cardano so we will be able to sign transactions with this private key.
   - `-out private-keys\your-key-name-here.private` says to save the file in the `private-keys` directory you just created with the file name `your-key-name-here.private`

   You will be prompted for a password.
   It if vial that you use a strong password to encrypt your private key, as the file will be living on your machine and is therefore vulnerable to being stolen.
   Encryption with a strong password will ensure that even if the file is stolen, the information will not be compromised.

   Some suggestions for a password:

   1. If you use a password manager, generate a new password using it.
   2. Pick a random phrase of at least 4 words, but six is better. You can
      either make it up (for example, to quote XKCD "correct horse battery staple"
      - obviously don't use that exact phrase), or you can use an online tool
      such as https://mdigi.tools/memorable-password/. Click the `phrase password`
      password type. Consider writing it down somewhere physically secure so you have a backup in case you forget it.

3. Verify the creation of the key with the password:

   Run the command

   ```powershell
   openssl pkey -in private-keys\your-key-name-here.private -pubout
   ```

   it will prompt you to enter your password. Do so to decrypt the file. It
   will print the public key (not sensitive information) to the PowerShell
   output.

4. Create offline backups of the private key - e.g. in USB sticks or external
   hard drives that are kept physically secure. This will prevent key loss in
   the case of hard drive failure.

### Step 3: Create a Certificate Signing Request

1. From PowerShell, run the command

   ```powershell
   openssl req -new -key private-keys\your-key-name-here.private -out private-keys\your-key-name-here.csr
   ```

   For convenience, it is a good idea to use the same base name for the CSR as
   for the private key (with the extension `.private` changed to `.csr`). That
   way you will know which private key is associated with which CSR.

   Enter your password to decrypt the key.

   You will be prompted to enter values for certain fields. The Head of
   security can inform you what you should put in these fields. For example:

   See [openssl.cnf](openssl.cnf) for the values you should use. 

   ```
   Country Name (2 letter code) [AU]:US
   State or Province Name (full name) [Some-State]:Colorado
   Locality Name (eg, city) []:Longmont
   Organization Name (eg, company) [Internet Widgits Pty Ltd]:Input Output Global
   Organizational Unit Name (eg, section) []:ICC - Mainnet
   Common Name (e.g. server FQDN or YOUR name) []:Your name here
   Email Address []:
   ```

   NOTE: where it says "Your name here", enter your real name.

   NOTE: leave the challenge password blank. It will be viewable in clear text in the CSR, which you will send to the head of security.

3. Inspect the CSR with the following command:

   ```powershell
   openssl req -in private-keys\your-key-name-here.csr -text -noout
   ```

   It will print the information you just entered along with the public key and
   signature used to verify that the request was signed by your private key.

4. Send your CSR to your head of security


## MacOS

### Step 1: Install necessary software

1. Open the "Terminal" application, e.g. via spotlight
2. Install the `Homebrew` package manager if you have not already done so: https://brew.sh/
   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```
3. Install `openssl` using Homebrew
   ```bash
   brew install openssl
   ```

### Step 2: Create an Ed25519 Private Key

1. Decide where you want to keep your private keys. You will need to remember
   where they are for future use. For example, you can keep them in a directory
   called `private-keys` in your user's home directory. We will proceed
   assuming this is what you want to do.

2. Open a Terminal window.
   a. Aside on working directories: By default, the terminal will open in your
      user's home directory which is commonly abbreviated as `~`. If you run the
      command `pwd` it will print out the absolute path of the present working
      directory. For example, here is what I see:
      ```bash
      pwd
      /Users/Jamie
      ```
      The working directory is the directory the terminal is currently
      "inside". You can change to a different working directory using the `cd`
      command. For our purposes, we will remain in the default `~` (home) directory.
3. Create the directory. The command `mkdir` is used to make a directory. In
   the terminal, run the command:
   ```bash
   mkdir ~/private-keys
   ```
   This command says to make a new directory called `~/private-keys`. Recall
   that `~` is short for your home directory, so on my machine this would
   create the directory `/Users/Jamie/private-keys`.

4. Create your private key. From the terminal, run the command:

   ```bash
   openssl genpkey -aes256 -algorithm ed25519 -out ~/private-keys/your-key-name-here.private
   ```

   With `your-key-name-here` replaced with some useful name, for example `cc-delegator` or `cc-voter`.
   Keep in mind that you may need to manage multiple keys in the future, so pick a naming scheme that will be easy to keep track of.
   For example, you could include the date in the name as well, e.g. `cc-delegator-2024-07-24` so you know which is the latest one.

   breaking this down:
   - `openssl genpkey` is a command used to generate private keys
   - `-aes256` says we want to encrypt the private key with the AES 256 cipher (widely considered to be one of the most secure symmetric ciphers)
   - `-algorithm ed25519` says to generate a key using the Ed25519 elliptic curve - this is the native algorithm for Cardano so we will be able to sign transactions with this private key.
   - `-out ~/private-keys/your-key-name-here.private` says to save the file in the `~/private-keys` directory you just created with the file name `your-key-name-here.private`

   You will be prompted for a password.
   It if vial that you use a strong password to encrypt your private key, as the file will be living on your machine and is therefore vulnerable to being stolen.
   Encryption with a strong password will ensure that even if the file is stolen, the information will not be compromised.

   Some suggestions for a password:

   1. If you use a password manager, generate a new password using it.
   2. Pick a random phrase of at least 4 words, but six is better. You can
      either make it up (for example, to quote XKCD "correct horse battery staple"
      - obviously don't use that exact phrase), or you can use an online tool
      such as https://mdigi.tools/memorable-password/. Click the `phrase password`
      password type. Consider writing it down somewhere physically secure so you have a backup in case you forget it.

3. Verify the creation of the key with the password:

   Run the command

   ```bash
   openssl pkey -in ~/private-keys/your-key-name-here.private -pubout
   ```

   it will prompt you to enter your password. Do so to decrypt the file. It
   will print the public key (not sensitive information) to the terminal
   output.

4. Create offline backups of the private key - e.g. in USB sticks or external
   hard drives that are kept physically secure. This will prevent key loss in
   the case of hard drive failure.

### Step 3: Create a Certificate Signing Request

1. From the terminal, run the command

   ```bash
   openssl req -new -key ~/private-keys/your-key-name-here.private -out ~/private-keys/your-key-name-here.csr
   ```

   For convenience, it is a good idea to use the same base name for the CSR as
   for the private key (with the extension `.private` changed to `.csr`). That
   way you will know which private key is associated with which CSR.

   Enter your password to decrypt the key.

   You will be prompted to enter values for certain fields. The Head of
   security can inform you what you should put in these fields. For example:

   See [openssl.cnf](openssl.cnf) for the values you should use. 

   ```
   Country Name (2 letter code) [AU]:US
   State or Province Name (full name) [Some-State]:Colorado
   Locality Name (eg, city) []:Longmont
   Organization Name (eg, company) [Internet Widgits Pty Ltd]:Input Output Global
   Organizational Unit Name (eg, section) []:ICC - Mainnet
   Common Name (e.g. server FQDN or YOUR name) []:Your name here
   Email Address []:
   ```

   NOTE: where it says "Your name here", enter your real name.

   NOTE: leave the challenge password blank. It will be viewable in clear text in the CSR, which you will send to the head of security.

3. Inspect the CSR with the following command:

   ```bash
   openssl req -in ~/private-keys/your-key-name-here.csr -text -noout
   ```

   It will print the information you just entered along with the public key and
   signature used to verify that the request was signed by your private key.

4. Send your CSR to your head of security

   NOTE: By default, the Finder app on MacOS tries to hide a lot of information
   about the file system's structure, so you may find it hard to locate the CSR
   file you just created. The way to navigate to the directory directly in Finder 
   is to hit ⌘-Shift-G to open a prompt that allows you to type in the exact
   path to navigate to (`~/private-keys`).

## Linux

### Step 1: Install necessary software

1. Open a terminal window
2. Install `openssl` using your distro's package manager if you haven't already
   done so.

### Step 2: Create an Ed25519 Private Key

1. Decide where you want to keep your private keys. You will need to remember
   where they are for future use. For example, you can keep them in a directory
   called `private-keys` in your user's home directory. We will proceed
   assuming this is what you want to do.

2. Open a terminal.
3. Create the directory.
   ```bash
   mkdir ~/private-keys
   ```
4. Create your private key.

   ```bash
   openssl genpkey -aes256 -algorithm ed25519 -out ~/private-keys/your-key-name-here.private
   ```

   With `your-key-name-here` replaced with some useful name, for example `cc-delegator` or `cc-voter`.
   Keep in mind that you may need to manage multiple keys in the future, so pick a naming scheme that will be easy to keep track of.
   For example, you could include the date in the name as well, e.g. `cc-delegator-2024-07-24` so you know which is the latest one.

   breaking this down:
   - `openssl genpkey` is a command used to generate private keys
   - `-aes256` says we want to encrypt the private key with the AES 256 cipher (widely considered to be one of the most secure symmetric ciphers)
   - `-algorithm ed25519` says to generate a key using the Ed25519 elliptic curve - this is the native algorithm for Cardano so we will be able to sign transactions with this private key.
   - `-out ~/private-keys/your-key-name-here.private` says to save the file in the `~/private-keys` directory you just created with the file name `your-key-name-here.private`

   You will be prompted for a password.
   It if vial that you use a strong password to encrypt your private key, as the file will be living on your machine and is therefore vulnerable to being stolen.
   Encryption with a strong password will ensure that even if the file is stolen, the information will not be compromised.

   Some suggestions for a password:

   1. If you use a password manager, generate a new password using it.
   2. Pick a random phrase of at least 4 words, but six is better. You can
      either make it up (for example, to quote XKCD "correct horse battery staple"
      - obviously don't use that exact phrase), or you can use an online tool
      such as https://mdigi.tools/memorable-password/. Click the `phrase password`
      password type. Consider writing it down somewhere physically secure so you have a backup in case you forget it.

3. Verify the creation of the key with the password:

   Run the command

   ```bash
   openssl pkey -in ~/private-keys/your-key-name-here.private -pubout
   ```

   it will prompt you to enter your password. Do so to decrypt the file. It
   will print the public key (not sensitive information) to stdout in PEM
   format.

4. Create offline backups of the private key - e.g. in USB sticks or external
   hard drives that are kept physically secure. This will prevent key loss in
   the case of hard drive failure.

### Step 3: Create a Certificate Signing Request

1. From the terminal, run the command

   ```bash
   openssl req -new -key ~/private-keys/your-key-name-here.private -out ~/private-keys/your-key-name-here.csr
   ```

   For convenience, it is a good idea to use the same base name for the CSR as
   for the private key (with the extension `.private` changed to `.csr`). That
   way you will know which private key is associated with which CSR.

   Enter your password to decrypt the key.

   You will be prompted to enter values for certain fields. The Head of
   security can inform you what you should put in these fields. For example:

   See [openssl.cnf](openssl.cnf) for the values you should use. 

   ```
   Country Name (2 letter code) [AU]:US
   State or Province Name (full name) [Some-State]:Colorado
   Locality Name (eg, city) []:Longmont
   Organization Name (eg, company) [Internet Widgits Pty Ltd]:Input Output Global
   Organizational Unit Name (eg, section) []:ICC - Mainnet
   Common Name (e.g. server FQDN or YOUR name) []:Your name here
   Email Address []:
   ```

   NOTE: where it says "Your name here", enter your real name.

   NOTE: leave the challenge password blank. It will be viewable in clear text in the CSR, which you will send to the head of security.

3. Inspect the CSR with the following command:

   ```bash
   openssl req -in ~/private-keys/your-key-name-here.csr -text -noout
   ```

   It will print the information you just entered along with the public key and
   signature used to verify that the request was signed by your private key.

4. Send your CSR file to your head of security
