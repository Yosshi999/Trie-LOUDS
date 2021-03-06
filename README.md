# Trie-LOUDS
[![Node.js CI](https://github.com/Yosshi999/trie-louds/actions/workflows/node.js.yml/badge.svg)](https://github.com/Yosshi999/trie-louds/actions/workflows/node.js.yml)
[![codecov](https://codecov.io/gh/Yosshi999/trie-louds/branch/master/graph/badge.svg?token=Y0XIT9GJS8)](https://codecov.io/gh/Yosshi999/trie-louds)

Readonly but memory-sufficient data structure for dictionaries by utilizing LOUDS.

## Install
```
$ npm install --save trie-louds
```

## Usage
```
const {ReadonlyTrieTree} = require("trie-louds");
const fs = require("fs");
const tree = ReadonlyTrieTree.fromKeywordList(["She", "sells", "seashells", "by", "the", "seashore"]);

console.log(tree.contains("She")); // true
console.log(tree.contains("she")); // false
console.log(tree.getWords("sea").words); // [ 'seashells', 'seashore' ] (search the words with given prefix)
console.log(tree.getValue("seashells")); // 2 (index of keywords)
console.log(tree.getValue("sell")); // null (not found)

console.log(tree.getWords("").words); // [ 'She', 'by', 'seashells', 'seashore', 'sells', 'the' ] (searched words are sorted)
const limited = tree.getWords("", 3);
console.log(limited.words); // [ 'She', 'by', 'seashells' ] (you can limit the number of searched words (default is 1000))
console.log(limited.hasMore); // true (if there are unsearched words due to limit, hasMore will be true)
// (and you can continue searching by calling getMoreWords with temporaryInfo)
console.log(tree.getMoreWords(limited.temporaryInfo).words); // [ 'seashore', 'sells', 'the' ]

fs.writeFileSync("tree.dat", tree.dump()); // You can dump the tree data.
const loadedTree = ReadonlyTrieTree.load(fs.readFileSync("tree.dat"));
console.log(loadedTree.getWords("sea").words); // [ 'seashells', 'seashore' ]
```

## Command
You can dump the tree data by command.
### example
1. run `trie-dump --input examples/keyword.txt --output examples/trie.dat`
2. then you have `trie.dat` in `examples/` folder.
3. execute:
```
const {ReadonlyTrieTree} = require("trie-louds");
const tree = ReadonlyTrieTree.loadFileSync("examples/trie.dat");
console.log(tree.getWords(""));
```
### enwiki trie tree
You can create the trie tree of wikipedia-en keywords.
```
> cat enwiki-20210220-pages-articles-multistream-index.txt | sed -e 's/.*://g' > enwiki-keywords.txt
> trie-dump --input ..\loudstest\enwiki-keywords.txt --output enwiki.dat
```
In this case, we can store 20993072 words in this trie tree and dump it.
The size of `enwiki-keywords.txt` is about 495MiB and the size of `enwiki.dat` is about 565MiB.

```
const {ReadonlyTrieTree} = require("trie-louds");
const {readFileSync} = require("fs");
const tree = ReadonlyTrieTree.load(readFileSync("./enwiki.dat"));
console.log(process.memoryUsage());
console.log(tree.getWords("Undertale"));

--- output ---
{ rss: 784404480,
  heapTotal: 10731520,
  heapUsed: 5436512,
  external: 761201137 }

{ words:
   [ 'Undertale',
     'Undertale (game)',
     'Undertale (video game)',
     'Undertale - Hopes and Dreams.ogg',
     'Undertale 2',
     'Undertale Combat Example.png',
     'Undertale Kickstarter Promotional Art.png',
     'Undertale character redirects to lists',
     'Undertale fandom',
     'Undertale soundtrack' ],
  hasMore: false }
```
And it takes about 750MiB when you load this trie tree on memory.

## TODO
- setup npm repo
