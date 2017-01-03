Add to the script to be profiled:

Start:

```shell
PS4=$'\\\011%D{%s%6.}\011%x\011%I\011%N\011%e\011'
exec 3>&2 2>/tmp/zshprof.$$.log
setopt xtrace prompt_subst
```

End:

```shell
unsetopt xtrace
exec 2>&3 3>&-
```

setup ocaml on osx:

```shell
brew install ocaml
brew install opam
brew info opam
eval `opam config env`
source ~/.opam/opam-init/init.zsh
opam update
opam install ocamlfind
```

Compile the tool:

```shell
ocamlfind ocamlopt -linkpkg -thread -package str Callgrind.ml ZshXtrace.ml ZshXtraceToCallgrind.ml -o "zshprof.bin"
```

run the zshprof tool on your trace output file

```shell
./zshprof.bin < /tmp/zshprof.57612.log > /tmp/zshprof.57612.callgrind
```

View the result:

```shell
#on linux
kcachegrind zsh.callgrind
# on osx
brew install qcachegrind --with-graphviz
qcachegrind /tmp/zshprof.57612.callgrind
```

Limitations:

* This approach is utterly broken when there is any parallellism. You'll have to fall back to zprof when that is the case.
* It seems kcachegrind doesn't like it when you use the same filename for different traces.. careful.

A quick check

```shell
time bash -li -c 'echo hi; exit;'
time zsh -li -c 'echo hi; exit;'
```
