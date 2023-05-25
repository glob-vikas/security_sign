# Security Release Review Automation

This is the repository for the SRRA, written in Python .

## Local development

### Prerequisites
**To run this lambdas, make sure you have direnv and pyenv setup in your local.**

#### Minimum Python version required `= 3.11.2`

#### Build & Activate Virtual Environment and install dependencies
From this source directory, create a virtual environment:
```
direnv allow .
```

This will generate a virtualenv for this module in `.direnv/`. Make sure this venv is active in your
development environment of choice. To activate it from the terminal, run:
```
source .direnv/bin/activate
pip install -r requirements.txt
