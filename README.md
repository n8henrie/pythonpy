# pythonpy

Forked from `MatthieuBizien/pythonpy`

```console
$ pipx install git+https://github.com/n8henrie/pythonpy
$ echo foo | py -x 'print(x + "bar")'
foobar
$ echo -e '1\n2\n3' | py -l 'sum(map(int, l))'
6
```

Makes for much easier piping. Comparison with `python3 -c`:

```console
$ echo -n foo | py -x 'x'
foo
$ echo -n foo | python3 -c 'import sys; x=sys.stdin.read(); print(x)'
foo
$ echo -ne '1\n2\n3' | py -l 'sum(map(int, l))'
6
$ echo -ne '1\n2\n3' | python3 -c 'import sys; l=[line.strip() for line in sys.stdin.readlines()]; print(sum(map(int, l)))'
6
```

### Float Arithmetic

```console
$ py '3 * 1.5'
4.5
```

### Automatic imports

```console
$ py 'math.exp(1)'
2.71828182846

$ py 'random.random()'
0.103173957713

$ py 'datetime.datetime.now?'
Help on built-in function now:

  now(...)
        [tz] -> new datetime with tz's local day and time.
```

### Lists are printed row by row

```console
$ py 'range(3)'
0
1
2

$ py '[range(3)]'
[0, 1, 2]
```

### `py -x 'foo(x)'` applies `foo` to each line of input

```console
$ py 'range(3)' | py -x 'int(x)*7'
0
7
14
```

#### Append `.txt` to each line of input

```console
$ py 'range(3)' | py -x 'x + ".txt"'
0.txt
1.txt
2.txt
```

#### Append `.txt` to every file in the directory

- sharp quotes are swapped out for single quotes
- single quotes handle spaces in filenames

```console
$ ls | py -x '"mv `%s` `%s`" % (x,x)' | sh -x
+ mv MANIFEST.in MANIFEST.in
mv: 'MANIFEST.in' and 'MANIFEST.in' are the same file
+ mv README.md README.md
mv: 'README.md' and 'README.md' are the same file
+ mv build build
mv: cannot move 'build' to a subdirectory of itself, 'build/build'
+ mv pythonpy pythonpy
mv: cannot move 'pythonpy' to a subdirectory of itself, 'pythonpy/pythonpy'
+ mv pythonpy.egg-info pythonpy.egg-info
mv: cannot move 'pythonpy.egg-info' to a subdirectory of itself, 'pythonpy.egg-info/pythonpy.egg-info'
+ mv setup.py setup.py
mv: 'setup.py' and 'setup.py' are the same file
+ mv test test
mv: cannot move 'test' to a subdirectory of itself, 'test/test'
+ mv tox.ini tox.ini
mv: 'tox.ini' and 'tox.ini' are the same file
```

#### Get only even numbers

```console
$ py 'range(8)' | py -x 'x if int(x)%2 == 0 else None'
0
2
4
6
```

### `py -fx 'predicate(x)'` filters rows satisfying a condition

#### Get only odd numbers:

```console
$ py 'range(8)' | py -fx 'int(x)%2 == 1'
1
3
5
7
```

#### Get words starting with "and"

```console
$ cat /usr/share/dict/words | py -fx 'x.startswith("and")' | head -5
and
andante
andante's
andantes
andiron
```

#### Get verbs starting with ba

```console
$ cat /usr/share/dict/words | py -fx 're.match(r"ba.*ing$", x)' | head -5
baaing
babbling
babying
babysitting
backbiting
```

#### Get long palindromes

```console
$ cat /usr/share/dict/words | py -fx 'x==x[::-1] and len(x) >= 5' | head -5
civic
deified
kayak
level
ma'am
```

### `py -l` will set `l = list(sys.stdin)`

#### Reverse the input

```console
$ py 'range(3)' | py -l 'l[::-1]'
2
1
0
```

#### Sum the input

```console
$ py 'range(3)' | py -l 'sum(int(x) for x in l)'
3
```

#### Count the lines of input

```console
$ py 'range(17)' | py -l 'len(l)'
17
```

#### Count words beginning with each letter

```console
$ cat /usr/share/dict/words | py -x 'x[0].lower()' | py -l 'collections.Counter(l).most_common(5)'
('s', 11327)
('c', 9521)
('p', 7659)
('b', 6068)
('m', 5922)
```

## Known issues

- WIP
- I'm not sure whether I'll worry about the bash completion
- Licensing: multiple authors, listed as MIT. <https://github.com/Russell91>
  seems to have disappeared, still owns `https://pypi.org/project/pythonpy/`.
  `/LICENSE` refers to my changes and modifications, with much gratitude to the
  original authors for all the heavy lifting!
