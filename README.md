# eterna-siegfried

Minimal container-image för Siegfried, byggd med [melange](https://github.com/chainguard-dev/melange) och [apko](https://github.com/chainguard-dev/apko) på [Wolfi](https://wolfi.dev).

Image: `ghcr.io/eterna-earkiv/siegfried:v1.11.9` och `latest` (linux/amd64, linux/arm64)

`melange.yaml` bygger `sf` från källkod (låst commit). Signaturerna laddas ned vid första start till `/data` (ändras med `SF_HOME`).

Imagen körs som icke-root (uid/gid 1000, samma som ETERNA) så att den kan läsa filerna i ETERNA:s storage. Data ligger under `/data` – volymer som monteras där måste ägas av uid 1000.

## Bygga lokalt

Kräver `melange`, `apko` och `docker`:

```sh
make            # bygger paket och image för x86_64 och laddar in i docker
make ARCH=aarch64
```

Utan installerade verktyg går det att köra dem via container:

```sh
make MELANGE="docker run --rm --privileged -v $PWD:/work -w /work cgr.dev/chainguard/melange:latest" \
     APKO="docker run --rm -v $PWD:/work -w /work cgr.dev/chainguard/apko:latest"
```

## Release

1. Uppdatera `VERSION` i `Makefile` (och versioner i `apko.yaml`/`melange`-filer).
2. Pusha en tagg `v<VERSION>`, t.ex. `git tag v1.11.9 && git push origin v1.11.9`.

GitHub Actions bygger paket för x86_64 och aarch64 på egna runners och publicerar en multi-arch-image till GHCR. Bygget kan också startas manuellt (*Run workflow*).

Paketen signeras med nyckeln i secret `MELANGE_RSA` (skapas med `melange keygen`).
