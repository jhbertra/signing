# Key Management for Delegators and Voters

## Step 1: Install necessary software

### MacOS instructions:

1. Open the "Terminal" application, e.g. via spotlight
2. Install the `Homebrew` package manager if you have not already done so: https://brew.sh/
   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```
3. Install `openssl` using Homebrew
   ```bash
   brew install openssl
   ```

### Linux:

TODO

### Windows:

TODO

## Step 2: Create a Ed25519 Private Key

1. Decide where you want to keep your private keys. You will need to remember
   where they are for future use.
   a. When you open a terminal by default, it will be in your user's default
   "home" directory. On Linux this will look something like `/home/<your-username>`.
   on MacOS, this will look something like `/Users/<your-username>`. On
   Windows, TODO. On MacOS and Linux, `~` is an abbreviation for this
   directory.
   b. One place you can consider keeping them that is easy to remember and easy
   to access from a terminal is a directory called `private-keys` in your home
   directory. If you want to do this, create the directory from the terminal
   using the command

   ```bash
   mkdir ~/private-keys
   ```

2. Create the private key with the command:

   ```bash
   openssl genpkey -aes256 -algorithm ed25519 -out ~/private-keys/key-name.private
   ```

   With `key-name` replaced with some useful name, for example `cc-delegator`
   or `cc-voter`. Keep in mind that you may need to manage multiple keys in the
   future, so pick a naming scheme that will be easy to keep track of. For
   example, you could include the date in the name as well, e.g.
   `cc-delegator-2024-07-24` so you know which is the latest one.

   breaking this down:
   - `openssl genpkey` is a command used to generate private keys
   - `-aes256` says we want to encrypt the private key with the AES 256 cipher
      (widely considered to be one of the most secure symmetric ciphers)
   - `-algorithm ed25519` says to generate a key using the Ed25519 elliptic
      curve - this is the native algorithm for Cardano.
   - `-out ~/private-keys/<key-name>.private` says to save the file in the
       `~/private-keys` directory you just created with the file name `<key-name>.private`

   You will be prompted for a password. It if vial that you use a strong
   password to encrypt your private key, as the file will be living on your
   machine and is therefore vulnerable to cyber attacks. Encryption with a
   strong password will ensure that even if the file is stolen, the information
   will not be compromised.

   Some suggestions for a password:

   1. If you use a password manager, generate a new password using it.
   2. Pick a random phrase of at least 4 words, but six is better. You can
      either make it up (for example, to quote XKCD "car horse staple battery"
      - obviously don't use that exact phrase), or you can use a random number
      generator and pick words from a dictionary using the number as an
      index.
   3. Consider writing it down somewhere physically secure if you go with
      option 2 so you have a backup in case you forget it.


3. Verify the creation of the key with the password:

   Run the command

   ```bash
   openssl pkey -in ~/private-keys/your-key-name-here.private -pubout
   ```

   it will prompt you to enter your password. Do so to decrypt the file.

## Step 3: Create a Certificate Signing Request

1. From the terminal, run the command

   ```bash
   openssl req -new -key ~/private-keys/your-key-name-here.private -out ~/private-keys/your-key-name-here.csr
   ```

   For convenience, it is a good idea to use the same base name for the CSR as
   for the private key (with the extension `.private` changed to `.csr`). That
   way you will know which private key is associated with which CSR.

   You will be prompted to enter values for certain fields. The Head of
   security can inform you what you should put in these fields.
