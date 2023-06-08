# protocol documentation



this is a long winded high level brain dump.


pogger is an archepelego of hubs servers that clients can connect to. these hubs are not all fully connected and their toplogy ebbs and flows at the whims hub operators. a hub's role is a fully self contained domain in which users exchange BEP0052 infohashes containing encrypted chat data.
every chat message is represented as a single encrypted file. all communication is done over yggdrasil.

each hub has a long term public ed448 signing public key used as a CA in addition to an yggdrasil address.

users on this hub are granted access to use the hub by the server issuing them a certificate signed by the server's signing key. this certificate is used to do tls peer authentication. the public key is given to the server by the user.

users speak bittorrent via tls using their tls certificates that the user issued them when downloading files.

the hub serves all torrent metainfo files required to fetch messages.

users have a profile data, which is a directory of files served via a BEP0052 style torrent.

the profile data has a public key and all metadata about the user, like profile picture, name, and anything else that may be provided in the future. the hub MAY seed this torrent if the user requests it to be. clients MUST be capable of seeding this data if the server does not store it for them.

the hub stores an index of each user's current profile infohash along with a signature of the username and infohash by the user who published the entry. to verify the validity of this data as another user, you download the public key file from the swarm given the infohash, and then check that the signature and pubkey matches.


posts made by users use a snowflake id as the filename with the file extension `.pogger`, file atttachments have their file extention suffixed with `.pogger`, so a file attachment `me.png` would use be seen as `slowflakeidgoeshere.png.pogger`.


the converstation is addressable via the BEP0052 infohash of a torrent containing every message. when a new message is made, the infohash is recacluated and then published to the hub by the user. this is gossiped to users who are susbcribed to the user publishing that message.

since BEP0052 uses a merkle tree this is relatively cheap to recompute if we cache the hash of every leaf in the tree we already caclulated. BEP0052 lets bittorrent clients data by 16KB block's hash so the net results is pretty efficient all things considered.

the hubs stores all infohashes that a user has given it, the user can update an existing infohash or create a new one. users use a stealth address for each subsciption to a different user to avoid excessive updates from converstations they dont care about.

in order to have a back and forth converstation two users must be subscribed to each other. each user will know each other's public keys and thus be able to decrypt posts from each other.

announce channels are also possible with this approach given that all users are able to decrypt the message.


the rest of the spec entails how the client displays the decrypted version of each of the post. that is where the fun begins.
