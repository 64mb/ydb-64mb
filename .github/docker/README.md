# Local YDB on Linux ARM64

Build from a full checkout on a native ARM64 Docker host. Plan for at least
80 GB of free space on the Docker builder, as for a [source build](../../BUILD.md).

```sh
revision=$(git rev-parse HEAD)
docker build --platform linux/arm64 \
  --build-arg YDBD_SOURCE=. --build-arg CLI_SOURCE=. --build-arg LOCAL_YDB_SOURCE=. \
  --build-arg DOCKER_FILES=.github/docker/files \
  --label "ydb.revision=$revision" \
  --label "org.opencontainers.image.revision=$revision" \
  --file .github/docker/Dockerfile --tag local-ydb:arm64 .

IMAGE=local-ydb:arm64 IMAGE_PLATFORM=linux/arm64 EXPECTED_REVISION="$revision" \
  bash .github/docker/tests/run_acceptance_tests.sh
```

The acceptance suite uses Linux host networking. Run it from the Linux Docker
host so that the test scripts can reach the database ports.

The **Build local YDB for ARM64** workflow uses a Linux ARM64 self-hosted runner
labeled `ydb-docker`. Once the workflow is on the default branch, dispatch it
from GitHub Actions to build, test, and publish
`ghcr.io/<owner>/local-ydb:arm64-<commit>`.
