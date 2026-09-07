# Django Quiz Generator

A Django application that turns uploaded study material into quizzes and flashcards using
**AWS Bedrock (Claude 3)**. Upload a document, choose a format and a question count, and the app
generates the questions, stores them against your account and tracks how you do on them.

## What it does

**Document upload** — accepts PDF, PPTX, DOCX, TXT and other common formats.

**AI question generation** — an `AWSBedrockClient` (`quizmake/aws_utils.py`) calls Bedrock with the
extracted text and parses the model's response into structured questions.

**Two quiz formats**
- *Flashcards* — question and answer pairs
- *Multiple choice* — a question, four options and a recorded correct answer

**Session tracking** — quizzes belong to a user, and attempts are recorded so performance can be
reviewed over time.

## Data model

```
Quiz            user, title, quiz_type, num_questions, timestamps
UploadedFile    quiz, file, filename, file_size, uploaded_at
Question        quiz, question_text, answer_text, options (JSON),
                correct_answer, order
```

## Applications

| App | Purpose |
|---|---|
| `quizmake` | The quiz generator — upload, Bedrock integration, quiz and question models |
| `weighttracker` | A separate weight-logging module |
| `home` | Landing pages and shared templates |

## Stack

Django · Python · AWS Bedrock (Claude 3) via `boto3` · gunicorn · SQLite in development

## Running it

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py runserver
```

AWS credentials are read from the environment — the application expects `AWS_ACCESS_KEY_ID`,
`AWS_SECRET_ACCESS_KEY`, `AWS_REGION` and `AWS_BEDROCK_MODEL`, and reads them through Django settings
via `os.getenv`. Never commit them.

Production is served under gunicorn; see `gunicorn.conf.py` and `gunicorn.service`.
