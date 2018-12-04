+++
title = "Effective Modern Python 2018"

[slides]
# Choose a theme from https://github.com/hakimel/reveal.js#theming
theme = "white"
+++

## Effective Modern Python 2018

[Ryo Takahashi](../..)

---

## Contents

- Type Annotation
- Data Classes
- Packaging and Dependency Management

---

## Type Annotation ![Python 3.5](https://img.shields.io/badge/python-3.5-blue.svg)

---

### Some benefits of type annotations

- Static code analysis prior to runtime
    - PyCharm (IntelliJ)
    - [mypy](http://mypy-lang.org/)
- Self-documenting
    - Code itself is a document
- Code completion

---

### [Chainer RNNLM Example](https://github.com/chainer/chainer/blob/master/examples/ptb/train_ptb.py)

```python
train, val, test = chainer.datasets.get_ptb_words()
```

- Type of `train`, `val`, `test`?
- What kind of operations can be done on these variables?

---

### 🤔

[![Image from Gyazo](https://i.gyazo.com/418e6304d4e0fa7c33a6f808e654beb0.png)](https://gyazo.com/418e6304d4e0fa7c33a6f808e654beb0)

---

### Go to Declaration

```python
def get_ptb_words():
    """Gets the Penn Tree Bank dataset as long word sequences.

    [...]

    Returns:
        tuple of numpy.ndarray: Int32 vectors of word IDs.
    """
    train = _retrieve_ptb_words('train.npz', _train_url)
    valid = _retrieve_ptb_words('valid.npz', _valid_url)
    test = _retrieve_ptb_words('test.npz', _test_url)
    return train, valid, test
```

- 😸 docstring
- 😿 IDEs can't parse complicated types

---

### Add Type Annotations

```python
from typing import Dict
def load_vocab(vocabfile: str) -> Dict[int, str]:
    [...]
```

```python
# typed!
vocab = load_vocab(vocabfile)
```

If you can't modify function declaration...

```python
# function of other libraries
def load_vocab(vocabfile):
    [...]
```

```python
from typing import Dict
# typed!
vocab: Dict[int, str] = load_vocab(vocabfile)
```

---

### 🤗

[![Image from Gyazo](https://i.gyazo.com/e590f96a32b8e861bca2ef3582f0fd70.png)](https://gyazo.com/e590f96a32b8e861bca2ef3582f0fd70)

---

## Data Classes ![Python 3.7](https://img.shields.io/badge/python-3.7-blue.svg)

---

### Easier Way to Write Classes 🤗

```python
from dataclasses import dataclass
from typing import Dict, Optional

@dataclass
class Vocabulary:
    padding_token: str
    oov_token: str
    max_vocab_size: int
    pretrained_files: Optional[Dict[str, str]] = None
```

- Similar to Scala's Case Classes

---

### Conventional Class 🤔

```python
class Vocabulary:
    def __init__(
        self,
        padding_token: str,
        oov_token: str,
        max_vocab_size: int,
        pretrained_files: Optional[Dict[str, str]] = None,
    ):
        self.padding_token = padding_token
        self.oov_token = oov_token
        self.max_vocab_size = max_vocab_size
        self.pretrained_files = pretrained_files

    def __eq__(self, other):
        if isinstance(self, other.__class__):
            return self.__dict__ == other.__dict__
        return False
```

---

- Documentation ([en](https://docs.python.org/3/library/dataclasses.html) / [ja](https://docs.python.org/ja/3/library/dataclasses.html))
- [attrs library](https://www.attrs.org/en/stable/index.html) is also recommended
    - more features than Data Classes
    - move faster

---

## Packaging and Dependency Management

- How to reproduce your environment?
- How to make your program `pip` installable?

---

Other languages have great tools

- Scala: `sbt`
- JavaScript: `npm`, `yarn`
- Rust: `cargo`
- Go: `dep`
- Python: ...🤔

---

### Ancienty Way 🤔

- Dump all installed packages and force to download them all:

```bash
$ pip freeze > requirements.txt
$ pip install -r requirements.txt
```

- Need to manage virtual environment at your own responsibility
    - Don't you have unnecessary dependency?
    - How is version compatibility of Python and libraries?
- Metadata management?

---

### Medieval Way 🤔

- Use standard [Setuptools](https://setuptools.readthedocs.io/en/latest/index.html)
    - *Messy* documentation
    - Need to write *messy* `setup.py`

<a href="https://gyazo.com/80f294c3dd355371cf7b07473394bf34"><img src="https://i.gyazo.com/80f294c3dd355371cf7b07473394bf34.png" alt="Image from Gyazo" width="300"/></a>

---

### Modern Way 🤗

- Use [Pipenv](https://pipenv.readthedocs.io/en/latest/), [Hatch](https://github.com/ofek/hatch), [Flit](https://flit.readthedocs.io/en/latest/), or [Poetry](https://poetry.eustace.io/)
    - Work as another `pip`
    - *Clean* documentation
- I personally recommend Poetry

```bash
$ poetry init
$ poetry add chainer  # equivalent to `pip install chainer`
$ poetry build  # make wheels
$ poetry publish  # upload to PyPI
                  # pip installable after this command 🤗
```

---

## Further Readings

- Type Annotation
    - [The other (great) benefit of Python type annotations](https://medium.com/@shamir.stav_83310/the-other-great-benefit-of-python-type-annotations-896c7d077c6b)
- Data Classes
    - [The Ultimate Guide to Data Classes in Python 3.7 – Real Python](https://realpython.com/python-data-classes/)
- Packaging and Dependency Management
    - [Python's New Package Landscape](http://andrewsforge.com/article/python-new-package-landscape/)
    - [How to Publish an Open-Source Python Package to PyPI – Real Python](https://realpython.com/pypi-publish-python-package/)