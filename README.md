# Flask CI/CD Pipeline

[![CI/CD](https://github.com/tbhcoding/flask-cicd-demo/actions/workflows/ci.yml/badge.svg)](https://github.com/tbhcoding/flask-cicd-demo/actions/workflows/ci.yml)

A minimal Flask service with a three-stage GitHub Actions pipeline that takes every push to `main` from tests to a running container on an Azure VM.

## Pipeline

| Stage | Runs | What it does |
|:--|:--|:--|
| `test` | on pushes to `main` and on pull requests | Installs dependencies on Python 3.12 and runs the pytest suite |
| `docker-build` | after `test` passes | Builds the Docker image |
| `deploy` | on `main` only, after `docker-build` | Connects to the Azure VM over SSH, pulls `main`, rebuilds the image, and restarts the container on port 80 |

The workflow is defined in [`.github/workflows/ci.yml`](.github/workflows/ci.yml).

## Deployment secrets

The deploy job reads three repository secrets: `SSH_PRIVATE_KEY`, `SSH_HOST` and `SSH_USER`. The VM needs Docker installed and a clone of this repository at `~/flask-cicd-demo`.

## Run locally

```bash
pip install -r requirements.txt
python -m pytest        # run the tests
python app.py           # serve at http://localhost:5000
```

To run it in Docker, where Gunicorn serves the app (see the [`Dockerfile`](Dockerfile)):

```bash
docker build -t flask-cicd-demo .
docker run -p 5000:5000 flask-cicd-demo
```
