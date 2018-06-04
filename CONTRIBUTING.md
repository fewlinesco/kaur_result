# Contributing

We welcome Pull Requests from anyone 👐

By participating in this project, you agree to abide by its [Code of Conduct](CODE_OF_CONDUCT.md).

Before submitting a Pull Request, please ensure the following:

1. Your code is covered by tests
2. Make sure all tests are passing
3. Only have one commit with a relevant message (you can rebase and squash all your commits into a single one)
4. Check that you're up to date by rebasing your branch with our `develop` branch
5. Do not forget to check that the code meets our standards:
   - `mix format` (if your version is >= `1.6`)
   - `MIX_ENV=test mix credo --strict`
   - `mix dialyzer`

## Setup

```
$ git clone https://github.com/fewlinesco/kaur_result.git
$ cd kaur_result
$ mix deps.get
$ mix test
```
