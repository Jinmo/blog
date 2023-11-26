## #4. aimbot

It's .exe so let's open IDA Pro: it injects some code to a game: "Cube 2: Sauerbraten". The first shellcode is decrypted after some setups and checks. After checking the decryption routine and dependencies, I ran a debugger, set the IP (Ctrl+N) to the decryption routine and dumped a shellcode.

After initial shellcode it decrypts another shellcode using hardcoded files from some other programs as the decryption keys (Bitcoin Wallet, Steam, ...) I googled or installed those programs to check the files.