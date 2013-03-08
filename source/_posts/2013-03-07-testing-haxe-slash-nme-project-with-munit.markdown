---
layout: post
title: "Testing Haxe/NME project with munit"
date: 2013-03-07 11:15
comments: true
published: false
categories: haxe,nme,tdd,games,munit
---

haxelib install munit
haxelib run munit
haxelib run munit ct
haxelib run munit ct Tile
ls
ls test/
ls src/
ls com
ls src/com/
haxelib run munit ct com.rradish.lazOr.Tile
haxelib run munit ct com/rradish/lazOr/Tile
haxelib run munit ct com/rradish/lazOr/Tile.hx
haxelib run munit ct ./test/com/rradish/lazOr/Tile.hx
mkdir -p test/com/rradish/lazOr/
haxelib run munit ct ./test/com/rradish/lazOr/Tile.hx
haxelib run munit ct ./test/com/rradish/lazOr/Tile
haxelib run munit ct test/com/rradish/lazOr/Tile
ls
haxelib run munit ct ./ test/com/rradish/lazOr/Tile
haxelib run munit ct ./ test/com/rradish/lazOr/Tile
haxelib run munit ct test/com/rradish/lazOr/Tile
haxelib run munit
haxelib run munit config
haxelib run munit ct test/com/rradish/lazOr/Tile
ls -
ls -a
vim .munit
cd test
ls
rm -fr test/
rm -fr com/
ls
rm -fr LazOr.hx
cd ..
haxelib run munit ct com/rradish/lazOr/Tile
ls test
ls test/com/rradish/lazOr/Tile.hx
haxelib run munit
haxelib run munittest
haxelib run munit test
haxelib run munit test
haxelib run munit test
haxelib run munit test
haxelib run munit test
cd test
haxelib run munit test
cd ..
haxelib run munit test
haxelib run munit test
haxelib run munit test
haxelib run munit test
haxelib run munit test
haxelib run munit test -as3
man alias
alias haxelib run munit test -as3 munitas3
alias munitas3 "haxelib run munit test -as3"
cat ~/.bashrc
alias munitas3="haxelib run munit test -as3"
munitas3
alias munit="haxelib run munit test -as3"
alias munitall="haxelib run munit test"
munitas3
alias munit="haxelib run munit test -as3"
alias munitall="haxelib run munit test"
munit
munita
munitall
haxelib run munit gen
ls
ls test
ls test/com/
haxelib run munit gen src/com/games/lazOr/game/LazOrGameController.hx
haxelib run munit help
munit
haxelib run munit ct com/rradish/lazOr/Tile
rm test/com/rradish/lazOr/TileTest.hx
rm test/com/rradish/lazOr/Tile.hx
haxelib run munit ct com/rradish/lazOr/TileTest
munit
munit
ls
cd test
ls
rm -fr com/
cd ..
haxelib run munit ct com.rradish.lazOrTest.TileTest

munit
munit
munit

Add nme and actuate libs to hxml file
Change flash version to 10 (or 11) on hxml file
create alias for tailing logs
