# dexDB

Decentralized exchange database

The concept:

Two files, one binary and one configuration, the latter required only if the user needs to change some default configuration (eg: Listen port)

The configuration file and database will be created on the first execution of the binary, similar to most wallets of Crypto currencies.

When you start the application, it will searching for other nodes that contain a copy of the database and will download it, after this step the synchronization will occur on demand. The goal of this process is to make api fully portable, easy to install, both on servers and desktops, and make the ecosystem resistant to censorship.

The initial version is scheduled for August 2019 and will transparently replace the api currently used.

The binaries will initially be made available for Linux and Windows and the source code will be made available on GitHub.
