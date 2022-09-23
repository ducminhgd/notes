---
type: shell
keywords:
- shell
- script
- funciton
---
# Shell Functions

## Ubuntu

Require: `figlet`, run `sudo apt-get install figlet` 

Edit your `~/.profile` file

## MacOS

Require: `figlet`, run `brew install figlet`

Should create and `.zsh_functions` file and add function into that file. In `.zshrc` add `source ~/.zsh_functions`.

## Functions

```shell
git-co() {
    figlet "Checkout all" 
    if [ -z "$1" ]; then
        dir=$(pwd)
    else
        dir="$1"
    fi
    for i in $(find "$dir" -maxdepth 3 -name .git -type d | rev | cut -c 6- | rev); do
        echo "════════════════════════════════════════════════════════════════════════════════════════════════════════"
        echo "Directory: $i | Branch: $2"
        git -C "$i" checkout "$2"
        echo "────────────────────────────────────────────────────────────────────────────────────────────────────────\n"
    done
}
git-reset() {
    figlet "Reset all"
    if [ -z "$1" ]; then
        dir=$(pwd)
    else
        dir="$1"
    fi
    for i in $(find "$dir" -maxdepth 3 -name .git -type d | rev | cut -c 6- | rev); do
        echo "════════════════════════════════════════════════════════════════════════════════════════════════════════"
        echo "Directory: $i | Branch: $(git -C "$i" rev-parse --abbrev-ref HEAD) | Mode: $2"
        git -C "$i" reset "$2"
        echo "────────────────────────────────────────────────────────────────────────────────────────────────────────\n"
    done
}
git-pull() {
    figlet "Pull all"
    if [ -z "$1" ]; then
        dir=$(pwd)
    else
        dir="$1"
    fi
    for i in $(find "$dir" -maxdepth 3 -name .git -type d | rev | cut -c 6- | rev); do
        echo "════════════════════════════════════════════════════════════════════════════════════════════════════════"
        echo "Directory: $i | Branch: $(git -C "$i" rev-parse --abbrev-ref HEAD)"
        git -C "$i" pull
        echo "────────────────────────────────────────────────────────────────────────────────────────────────────────\n"
    done
}
git-sync() {
    figlet "Sync all"
    if [ -z "$1" ]; then
        dir=$(pwd)
    else
        dir="$1"
    fi
    for i in $(find "$dir" -maxdepth 3 -name .git -type d | rev | cut -c 6- | rev); do
        echo "════════════════════════════════════════════════════════════════════════════════════════════════════════"
        echo "Directory: $i | Branch: $(git -C "$i" rev-parse --abbrev-ref HEAD)"
        remote=origin; for brname in `git -C "$i" branch -r | grep $remote | grep -v master | grep -v HEAD | awk '{gsub(/^[^\/]+\//,"",$1); print $1}'`; do
            git -C $i branch --track $brname $remote/$brname || true;
            git -C $i checkout $brname && git -C $i pull;
        done
        echo "────────────────────────────────────────────────────────────────────────────────────────────────────────\n"
    done
}
```

```shell
trivyscan() {
    docker run --rm aquasec/trivy:latest image --no-progress --exit-code 1 --no-progress --severity CRITICAL $1
}
```

```shell
git-set-personal() {
    echo "Set git user to giaduongducminh@gmail.com"
    git config --local user.name "Giã Dương Đức Minh"
    git config --local user.email "giaduongducminh@gmail.com"
}
```