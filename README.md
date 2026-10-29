# komeet-ai-stack-releases

Builds publiés de la stack IA des projets Komeet : skills `komeet-*`, scripts de poste (`bin/`), modèles (`project/`, `templates/`) et `CHANGELOG.md`. Le code source et son historique sont dans un repo privé ; ce repo ne contient que des releases.

Chaque release correspond à un tag `vX.Y.Z` de la stack et porte deux fichiers :

- `komeet-ai-stack-vX.Y.Z.tar.gz` : le contenu de la stack à ce tag ;
- `komeet-ai-stack-vX.Y.Z.tar.gz.sha256` : son empreinte.

Les releases sont publiées par une GitHub Action du repo privé et ne sont jamais réécrites. Ne rien pousser ici à la main.

## Récupérer une version

Le tarball s'extrait dans le cache où `scripts/ai/stack` cherche la stack ; il ne la clone alors plus. Depuis la racine d'un projet qui a `ai-stack.toml` :

```bash
V=$(sed -n '/^\[stack\]/,/^\[/p' ai-stack.toml | grep -E '^version' | cut -d'"' -f2)
base="https://github.com/wenabiOfficial/komeet-ai-stack-releases/releases/download/$V"
tmp=$(mktemp -d)
curl -fsSL -o "$tmp/stack.tar.gz" "$base/komeet-ai-stack-$V.tar.gz"
echo "$(curl -fsSL "$base/komeet-ai-stack-$V.tar.gz.sha256" | cut -d' ' -f1)  $tmp/stack.tar.gz" | sha256sum -c -
mkdir -p ~/.cache/komeet-ai-stack/$V
tar -xzf "$tmp/stack.tar.gz" -C ~/.cache/komeet-ai-stack/$V
scripts/ai/stack doctor
```

Sur macOS, `shasum -a 256 -c -` remplace `sha256sum -c -`.
