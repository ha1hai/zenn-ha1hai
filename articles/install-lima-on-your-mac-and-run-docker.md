---
title: 'MacにLimaをインストールしてDockerを実行する'
emoji: '🐈'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: [lima, docker]
published: false
---

Mac に Lima をインストールして Docker を実行します。

## Docker インストール

```zsh
brew intall docker
```

```zsh
# 確認
docker --version
# Docker version 27.3.1, build ce1223035a
```

## Lima インストール

```zsh
brew install lima
```

```zsh
limactl --version
# limactl version 0.23.2

# Help
limactl help
```

## Lima VM を起動する

`Proceed with the current configuration`を選択する。

```zsh
limactl start template://docker
#  Creating an instance "default"  [Use arrows to move, type to filter]
# >  Proceed with the current configuration
#   Open an editor to review or modify the current configuration
#   Choose another template (docker, podman, archlinux, fedora, ...)
#   Exit
```

## Lima VM 一覧

```zsh
limactl list
# NAME       STATUS     SSH                VMTYPE    ARCH       CPUS    MEMORY    DISK      DIR
# default    Running    127.0.0.1:60022    qemu      aarch64    4       4GiB      100GiB    ~/.lima/default
```

## Dockr

```zsh
export DOCKER_HOST=$(limactl list default --format 'unix://{{.Dir}}/sock/docker.sock')
docker run --rm hello-world
```

## Lima Help

```zsh
limactl help
```

## 参考 URL

- [Lima](https://github.com/lima-vm/lima/blob/master/README.ja.md)
