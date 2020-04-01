# CI/CD Guide

## Philosophy

TODO

## Best practise

### with Travis CI

the simplest pipeline should be:

commit/push code ->  run test by CI -> notification (slack) if test failed

the following config file is the simple pipeline travis CI configuration file:

`.travis.yml` 

```yaml
language: python
python:
  - "3.6"
script: python -m unittest discover -s ./tests
notifications:
  slack:
    rooms:
      - secure: <encryped_secure_token>
    on_success: never
    on_failure: always
```