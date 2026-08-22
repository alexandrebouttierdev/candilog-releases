# candilog-releases

Ce dépôt public construit et publie les paquets desktop de
`alexandrebouttierdev/candilog-kotlin`. Le workflow produit :

- Linux : `.deb` et `.rpm` ;
- macOS : `.dmg` ;
- Windows : `.exe` et `.msi` ;
- `candilog-checksums.txt` et sa signature Ed25519
  `candilog-checksums.txt.sig`.

Chaque paquet existe sous un nom stable `latest` et un nom contenant la version produit.
L'application Candilog refuse l'installation automatique si la signature du manifeste, le
nom exact de l'asset ou son SHA-256 ne correspond pas.

## Secrets requis

- `CANDILOG_SOURCE_TOKEN` : accès en lecture au dépôt source Kotlin privé ;
- `CANDILOG_RELEASE_SIGNING_KEY_B64` : contenu PEM de la clé privée Ed25519, encodé en
  base64 sur une seule ligne.

La clé privée ne doit jamais être ajoutée à Git. Le job de publication la matérialise dans
le répertoire temporaire du runner avec le mode `600`, la supprime par `trap` et échoue si le
secret est absent. La clé publique correspondante est embarquée dans
`core.updater.Updater.RELEASE_SIGNING_PUBLIC_KEY` du dépôt Kotlin.

## Déclenchement

Un push sur `master` de `candilog-kotlin` envoie un `repository_dispatch` contenant le SHA
source, à condition que le secret `CANDILOG_RELEASES_TOKEN` y soit configuré. Un lancement
manuel accepte aussi une branche ou un SHA. Une version déjà publiée provoque un échec
explicite afin de ne jamais remplacer silencieusement des assets signés.
