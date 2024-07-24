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

