# Online Course Platform with Assessment Feature

A full-stack **Django** web application that provides an online learning platform where users can browse courses, enroll in them, and take auto-graded multiple-choice assessments. Built as a capstone project for the IBM Full Stack Cloud Development course.

---

## 📋 Table of Contents

- [Project Overview](#project-overview)
- [Key Features](#key-features)
- [Skills & Technologies](#skills--technologies)
- [Architecture Overview](#architecture-overview)
- [Database Schema](#database-schema)
- [Project Structure](#project-structure)
- [Installation & Setup](#installation--setup)
- [Running the Application](#running-the-application)
- [API Endpoints](#api-endpoints)
- [Deployment](#deployment)
- [License](#license)

---

## Project Overview

This platform allows:

- **Instructors** to create and manage courses with structured lessons and assessments.
- **Learners** to browse and enroll in courses, read lesson content, and take multiple-choice exams.
- **Automated grading** to evaluate exam submissions and provide instant pass/fail feedback with per-question breakdowns.

The project demonstrates a complete cloud-ready Django application including ORM-based database design, class-based and function-based views, user authentication, and cloud deployment configuration.

---

## Key Features

- 🔐 **User Authentication** — Registration, login, and logout with Django's built-in auth framework
- 📚 **Course Catalog** — Browse courses sorted by total enrollment, with images and descriptions
- 🎓 **Enrollment System** — Support for Audit, Honor, and BETA enrollment modes
- 📝 **Structured Lessons** — Ordered lesson content per course
- 📊 **Assessment Engine** — Multiple-choice questions with per-question grading weights
- ✅ **Auto-Grading** — Instant score calculation; pass threshold set at 80%
- 🎨 **Responsive UI** — Bootstrap 4 front-end with color-coded answer feedback
- 🛡️ **CSRF Protection** — All forms protected against cross-site request forgery
- ☁️ **Cloud-Ready Deployment** — Configured for IBM Cloud Foundry and Heroku

---

## Skills & Technologies

### Backend
| Technology | Purpose |
|---|---|
| **Python 3.8** | Core programming language |
| **Django 4.2** | Web framework (MVC/MVT pattern, ORM, auth, admin) |
| **Gunicorn 20.1** | Production WSGI HTTP server |
| **Pillow ≥ 10.3** | Image uploading and processing for course images |
| **Jinja2 ≥ 3.1** | Template engine (used alongside Django templates) |
| **aiohttp ≥ 3.9** | Asynchronous HTTP client |

### Frontend
| Technology | Purpose |
|---|---|
| **Bootstrap 4.5** | Responsive CSS framework |
| **jQuery 3.5** | DOM manipulation and AJAX |
| **Popper.js** | Tooltip and dropdown positioning |
| **Django Template Language** | Server-side HTML rendering |

### Database
| Technology | Purpose |
|---|---|
| **SQLite3** | Default development database |
| **Django ORM** | Database abstraction layer — compatible with PostgreSQL and MySQL |

### DevOps & Cloud
| Technology | Purpose |
|---|---|
| **IBM Cloud Foundry** | Primary deployment target (`manifest.yml`) |
| **Heroku** | Alternative deployment (`Procfile`) |
| **Python Buildpack** | Cloud runtime for the Django application |
| **Staticfile Buildpack** | Dedicated static file serving on IBM Cloud |

### Development Practices
- **MVT (Model-View-Template)** design pattern
- **Class-based views** (`ListView`, `DetailView`) and function-based views
- **Database migrations** for schema versioning
- **Django Admin** panel for content management
- **Many-to-Many relationships** via intermediary models (Enrollment, Submission)
- **Foreign Key constraints** with cascading deletes

---

## Architecture Overview

```
Browser  →  Django URL Router  →  Views (CBV / FBV)
                                      ↓
                               Django ORM  →  SQLite3 / PostgreSQL / MySQL
                                      ↓
                               Django Templates  →  Bootstrap UI  →  Browser
```

The app follows Django's **MVT** pattern:

- **Models** — Define the database schema and relationships
- **Views** — Handle HTTP requests, business logic, and ORM queries
- **Templates** — Render HTML with Bootstrap styling

Static assets and media uploads are served via Django's static files framework, with a separate `staticfile_buildpack` service on IBM Cloud Foundry for production.

---

## Database Schema

### ER Diagram

![Onlinecourse ER Diagram](https://github.com/ibm-developer-skills-network/final-cloud-app-with-database/blob/master/static/media/course_images/onlinecourse_app_er.png)

### Models

| Model | Description | Key Fields |
|---|---|---|
| `Instructor` | Instructor profiles | `user` (FK), `full_time`, `total_learners` |
| `Learner` | Student profiles | `user` (FK), `occupation`, `social_link` |
| `Course` | Online courses | `name`, `image`, `description`, `pub_date`, `instructors` (M2M), `total_enrollment` |
| `Lesson` | Ordered course content | `title`, `order`, `course` (FK), `content` |
| `Enrollment` | User ↔ Course junction | `user` (FK), `course` (FK), `date_enrolled`, `mode`, `rating` |
| `Question` | Exam questions | `course` (FK), `content`, `grade` |
| `Choice` | Answer options | `question` (FK), `content`, `is_correct` |
| `Submission` | Exam submissions | `enrollment` (FK), `choices` (M2M) |

**Key relationships:**
- A `Course` has many `Lesson` objects and many `Question` objects
- A `Question` has many `Choice` objects; each `Choice` is marked correct or incorrect
- An `Enrollment` links a `User` to a `Course`; a `Submission` is made per `Enrollment` and records which `Choice` objects were selected

---

## Project Structure

```
tfjzl-final-cloud-app-with-database/
├── manage.py                          # Django management utility
├── requirements.txt                   # Python dependencies
├── runtime.txt                        # Python runtime (3.8.13)
├── Procfile                           # Heroku deployment config
├── manifest.yml                       # IBM Cloud Foundry deployment config
├── myproject/                         # Django project package
│   ├── settings.py                    # App settings (DB, static files, installed apps)
│   ├── urls.py                        # Root URL configuration
│   ├── wsgi.py                        # WSGI entry point
│   └── asgi.py                        # ASGI entry point
├── onlinecourse/                      # Main Django application
│   ├── models.py                      # ORM models (Course, Lesson, Enrollment, etc.)
│   ├── views.py                       # Views (enrollment, submission, grading)
│   ├── urls.py                        # App URL routing
│   ├── admin.py                       # Django admin registrations
│   ├── migrations/                    # Database migration files
│   └── templates/onlinecourse/        # Bootstrap HTML templates
│       ├── course_list_bootstrap.html
│       ├── course_detail_bootstrap.html
│       ├── exam_result_bootstrap.html
│       ├── user_login_bootstrap.html
│       └── user_registration_bootstrap.html
└── static/                            # Static & media files
    └── media/course_images/           # Uploaded course images
```

---

## Installation & Setup

### Prerequisites

- Python 3.8+
- `pip`
- (Optional) `virtualenv` or `venv`

### Steps

```bash
# 1. Clone the repository
git clone https://github.com/rafael-a-g-n/tfjzl-final-cloud-app-with-database.git
cd tfjzl-final-cloud-app-with-database

# 2. Create and activate a virtual environment
python -m venv venv
source venv/bin/activate        # Linux / macOS
venv\Scripts\activate           # Windows

# 3. Install dependencies
pip install -r requirements.txt

# 4. Apply database migrations
python manage.py makemigrations
python manage.py migrate

# 5. Create a superuser for the admin panel (optional)
python manage.py createsuperuser

# 6. Collect static files
python manage.py collectstatic
```

---

## Running the Application

```bash
python manage.py runserver
```

Open your browser and navigate to:

| Page | URL |
|---|---|
| Course List | http://127.0.0.1:8000/onlinecourse/ |
| Django Admin | http://127.0.0.1:8000/admin/ |
| Login | http://127.0.0.1:8000/onlinecourse/login/ |
| Register | http://127.0.0.1:8000/onlinecourse/registration/ |

---

## API Endpoints

| Method | URL | View | Description |
|---|---|---|---|
| GET | `/onlinecourse/` | `CourseListView` | List top 10 courses by enrollment |
| GET | `/onlinecourse/<pk>/` | `CourseDetailView` | Course detail and lessons |
| POST | `/onlinecourse/<course_id>/enroll/` | `enroll()` | Enroll authenticated user in a course |
| POST | `/onlinecourse/<course_id>/submit/` | `submit()` | Submit exam answers |
| GET | `/onlinecourse/course/<course_id>/submission/<submission_id>/result/` | `show_exam_result()` | Display graded exam result |
| GET/POST | `/onlinecourse/registration/` | `registration_request()` | User registration |
| GET/POST | `/onlinecourse/login/` | `login_request()` | User login |
| POST | `/onlinecourse/logout/` | `logout_request()` | User logout |

---

## Deployment

### IBM Cloud Foundry

```bash
# Authenticate and push both the app and static file server
ibmcloud cf push
```

The `manifest.yml` defines two Cloud Foundry applications:
- `onlinecourse` — Django app using `python_buildpack` (128 MB)
- `onlinecourse-nginx` — Static file server using `staticfile_buildpack` (128 MB)

### Heroku

```bash
heroku create
git push heroku main
heroku run python manage.py migrate
```

The `Procfile` starts the app with Gunicorn:
```
web: gunicorn myproject.wsgi
```

### Database Configuration

The default database is SQLite3. To switch to PostgreSQL or MySQL, update `DATABASES` in `myproject/settings.py` and install the appropriate adapter (`psycopg2` or `mysqlclient`).

---

## License

This project is licensed under the **Apache License 2.0**. See the [LICENSE](LICENSE) file for details.
