# Runtimes Python

The Python that TextMate's bundle commands run on. Optional, because a minority of bundles need Python, and installing it is how a person says they are one of them.

macOS ships Python 3.9.6 at `/usr/bin/python3`, four years old and forbidden to install into, and nothing at all at `/usr/bin/python`. So a bundle command asking for `#!/usr/bin/env python` cannot start, which was the state of every Python command in every bundle until this existed. This bundle carries [uv](https://docs.astral.sh/uv/), Astral's Python manager, and the resolver the application asks.

The protocol the resolver answers in is described in the [Runtimes](https://github.com/textmate3/runtimes.tmbundle) bundle, which this one depends on.

## Support/bin

- `uv`: Astral's release for Apple silicon, with its licenses under `Support/licenses` and its version in `Support/VERSIONS`.
- `python_runtime <version>`: the Python for bundle commands. Finds the version among uv's own installs, installs it through uv when it is not there, and stands in with the newest Python uv manages when the install cannot happen.

  It answers with the interpreter rather than a directory, which is the one place it differs from the Ruby resolver. `uv python find` answers that way, and a Python's layout varies more than a Ruby's.

  Every lookup passes `--managed-python`, so only uv's own installs can ever be answered with. This is the other genuine difference between the two languages: rv does not know the system Ruby, so the Ruby resolver could not reach it by accident, while uv finds `/usr/bin/python3` and Xcode's copy readily. The system Python has to be excluded on purpose, here and again in the application.

A person's own `uv` is preferred over this one, so a Python TextMate installs shows up in their own list.

## Tests

From the bundle's directory:

```sh
ruby Support/tests/python_runtime_tests.rb
```

The tests run the resolver against a stand-in `uv` under `Support/tests/fixtures`, so they need no network and touch no real Python. The stub answers an unmanaged Python when `--managed-python` is absent, so a resolver that forgets the flag fails the tests the way the real uv would succeed: with a system Python.
