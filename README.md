# Elixir coverage feedback comment

This action gets the output of `mix test --cover`, treats it, and creates a feedback message in the pull request of origin. It also checks if the coverage reaches the minimum configured in the action, and exits with an error if it doesn't.

By default, this action assumes that you are using the Elixir's default coverage tool. But it also supports [ExCoveralls](https://github.com/parroty/excoveralls), and if you preferes it, just add the `coverage_tool` configuration inside the `with` option specifying it.

![image](https://user-images.githubusercontent.com/10376340/173872693-bf42c8b4-92c8-4332-840a-e935fb8cb836.png)

## Usage example

`.github/workflows/test.yml`

```yaml
on:
  pull_request:

jobs:
  test:
    runs-on: ubuntu-latest
    name: Tests & Checks
    env:
      MIX_ENV: test
    services:
      db:
        image: postgres:14-alpine
        ports: ["5432:5432"]
        env:
          POSTGRES_DB: project_test
          POSTGRES_USER: project
          POSTGRES_PASSWORD: mycoolpassword
    steps:
      - uses: actions/checkout@v2
      - uses: erlef/setup-elixir@885971a72ed1f9240973bd92ab57af8c1aa68f24
        with:
          elixir-version: "1.13.1"
          otp-version: "24.1.7"
      - name: Get dependencies
        run: mix deps.get
      - name: Code analyzers
        run: |
          mix format --check-formatted
          mix credo --strict
          mix compile --warnings-as-errors
      - name: Tests and coverage
        uses: josecfreittas/elixir-coverage-feedback-action@v0.1
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          coverage_threshold: 80
          # coverage_tool: excoveralls
```
