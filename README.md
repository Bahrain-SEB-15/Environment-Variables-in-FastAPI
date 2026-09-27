# Setting Up Environment Variables with FastAPI

`python-dotenv` allows your FastAPI application to load variables from a local `.env` file into `os.environ` during development.

## 1. Install `python-dotenv`

Install the package in your Python environment:

```bash
pipenv install python-dotenv
```

## 2. Load the `.env` file in `main.py`

Import `load_dotenv` and call it near the beginning of `main.py`:

```python
from dotenv import load_dotenv

load_dotenv()  # reads variables from a .env file and sets them in os.environ

# Code of your application, which uses environment variables
# (e.g. from os.environ or os.getenv) as if they came from the actual environment.
```

`load_dotenv()` should run **before code that attempts to read the environment variables**.

For example:

```python
from dotenv import load_dotenv

load_dotenv()

from fastapi import FastAPI

# Application code...
```

A basic project structure would look like:

```text
.
├── .env
└── main.py
```

If your project uses a different structure, the `.env` file can be located elsewhere, but make sure `load_dotenv()` can find it or provide the appropriate path explicitly.

## 3. Make sure your application reads the correct variable

If your project has a configuration file such as `config/environment.py`, update it to use the name of the environment variable defined in your `.env` file.

For example:

```python
DATABASE_URL = os.getenv("DATABASE_URL")
```

Make sure the name passed to `os.getenv()` matches the variable name in `.env` exactly.

For example, if your `.env` contains:

```env
DATABASE_URL=postgresql://...
```

then your Python code should use:

```python
os.getenv("DATABASE_URL")
```

If your project previously used a different name, such as `DB_URL`, update your development `.env` file accordingly:

```env
DATABASE_URL=postgresql://...
```

> Many starters already do this; adjust to your project's structure if needed.

## 4. Verify the configuration

At this point, the flow should be:

```text
.env
  ↓
load_dotenv()
  ↓
os.environ
  ↓
os.getenv("DATABASE_URL")
  ↓
DATABASE_URL
```

The important part is that `load_dotenv()` runs before `environment.py` (or any other configuration code) attempts to retrieve the variables.

### Example

`.env`:

```env
DATABASE_URL=postgresql://user:password@localhost/database
```

`main.py`:

```python
from dotenv import load_dotenv

load_dotenv()

from fastapi import FastAPI
from config.environment import DATABASE_URL

app = FastAPI()

# Use DATABASE_URL in your application...
```

`config/environment.py`:

```python
import os

DATABASE_URL = os.getenv("DATABASE_URL")
```

> **Note:** Do not commit your `.env` file if it contains secrets or other sensitive configuration. Make sure it is included in your `.gitignore`.
