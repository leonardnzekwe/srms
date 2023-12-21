# Netrobase SRMS REST API DOCS

Netrobase SRMS is a Student Result Management System. I hope to explain its application logic and how to interact with the API - [srms-api.netrobase.dev](https://srms-api.netrobase.dev), with few details in the frontend implementation [srms.netrobase.dev](https://srms.netrobase.dev). This could be handy whilst building your own robust and global student result management system (or consider using this one), this is actually the API documentation as it is in the codebase (private repo) and official documentation - [srms-docs.netrobase.dev](https://srms-docs.netrobase.dev). The codebase powers multiple schools' result portals, hence it is quite robust.

## About Dev:
* Name: Leonard Nzekwe
* Github: [leonardnzekwe](https://github.com/leonardnzekwe)
* Linkedin: [leonardnzekwe](https://www.linkedin.com/in/leonardnzekwe)
* X: [leonardnzekwe](https://x.com/leonardnzekwe)
* Hashnode Blog: [Leo's Nuggets](https://leonardnzekwe.hashnode.dev)

## Technologies:
* Frontend [srms.netrobase.dev](https://srms.netrobase.dev): Typescript, Nextjs, App Router, NextAuth, TailwindCSS.

* Backend [srms-api.netrobase.dev](https://srms-api.netrobase.dev): Python, Django, Django Rest Framework.


## BASE URLS:

* For Schools: [**srms-api.schooldomain.com**](http://srms-api.schooldomain.com)

* For Netrobase: [**srms-api.netrobase.dev**](http://srms-api.netrobase.dev)


## API End Points:

* `/` - \[GET\] API Index


### Auth Endpoints:

These Endpoints handle Authentications for all users.

* `/auth/` - \[GET\] Auth Index View

* `/auth/login/` - \[POST\] Login Route

* `/auth/logout/` - \[POST\] Logout Route

* `/auth/password/change/` - \[POST\] Password Change (From Dashboard)


> To change the user's password from the Django Admin, use the User Model Form on the Django admin dashboard, don't edit it directly from its instances (Student, Teacher, Dean). But you can use either of them for creation. The password hashing is handled properly during creation. But for password change use the User Password Change Form.

> It's only these two endpoints `/` and `/auth/` that doesn't require authentication. Most of the endpoints do not just require authentication but also some set of permissions.

> Interact with the platform as an admin. Use the above auth endpoints to log in or use the `/admin/` endpoint. You can create deans, teachers, students and their results to test out the platform. The login credentials are Username: `admin` and Password: `admin_netrobase`.

### Teachers Endpoints \[In Beta\]:

These endpoints require authentication and instance-level permissions.

* `/teachers/` - \[GET\] View all Teachers with classes and subjects each of them teach. This will be used by the Dean.

* `/teachers/<int:pk>/` - \[GET\] View a Teacher's data with classes and subject he/she teaches.

* `/teachers/<int:pk>/update` - \[PUT\] Update a Teacher's classes and subjects m2m.

* `/teachers/scoresheets/` - \[GET\] View a list of compulsory subjects for every class. \[`IsAuthenticated`, `DoesNotRequirePermissions`\]

* `/teachers/scoresheets/<int:subject_id>/` - \[GET\] \[PUT\] Get or Put multiple students' Result entries for a particular year, term, class and subject. These will be passed in as query parameters. This could be used by a teacher and the Dean.


> The teacher's scoresheet can update multiple students' Result entries for a particular subject in a particular year, term and class at a go. This is used to update students' result entries in a particular subject, year, term and class by a Teacher and possibly view/edit by the Dean too.

> The `teacher/int:pk/` endpoint could be used by both the Dean and the Teacher to view the teacher's record bio, with the list of classes and subjects the teacher is associated with.

> The `teacher/int:pk/update/` endpoint is used to update the class and subject the teacher is associated with. Also when a teacher logs in and adds or removes a subject/class he/she is associated with, the teacher's classes and subjects list fields get updated.

```json
// Sample View of teachers list endpoint. The teacher structure is same as a teacher's detail view.
[
    {
        "id": 19,
        "username": "sophia_williams_019@nggs",
        "first_name": "Sophia",
        "last_name": "Williams",
        "classes": [
            {
                "id": 1,
                "class_name": "Primary 1"
            }
        ],
        "subjects": [
            {
                "id": 1,
                "subject_name": "English"
            },
            {
                "id": 2,
                "subject_name": "Mathematics"
            }
        ]
    },
    // more teachers
]
```

```json
// View of Teacher Class and Subjects Update JSON
{
    "classes": [
        1, 2, 4
        // more class id's
    ],
    "subjects": [
        1, 2, 4
        // more subject id's
    ]
}
```

### Students Endpoints \[In Beta\]:

These endpoints require authentication and instance-level permissions.

* `/students/` - \[GET\] View students and their reports for a particular year, class and term. These will be passed in as query parameters. This is for the Dean.

* `/students/<int:student_id>/` - \[GET\] View a specific student and his/her result for a particular year, class and term. These will be passed in as query parameters. This is specifically for students, and can also be viewed by teachers and the Dean.

* `/students/<int:student_id>/update/` - \[PUT\] Update a specific student result for a particular year, class, and term. These will be passed in as query parameters. This would be used by the Academic Dean.

* `/students/subjects/<int:subject_id>/add/` - \[GET\] \[PUT\] View/Add a subject to multiple students' current\_subjects m2m.

* `/students/subjects/<int:subject_id>/remove/` - \[GET\] \[PUT\] View/Remove a subject from multiple students' current\_subjects m2m.


> A student should only be removed or added from a subject based on their current class. So the feature should be accessible on the front end just when the year returned by the session is the same as the year in the request/selection. Also, the feature should not be accessible in annual UI rendering views.

> The student update endpoint can only update one student at a go but can update the student's one or multiple subjects for a particular year, term and class at once. This is used to update a particular student's multiple subjects at a go by the Dean.

```json
// View of Students Current Subjects PUT (add, remove) JSON
{
    "students": [
        1, 2, 4
        // more student id's
    ],
}
```

```json
// View of Students Current Subjects GET (add, remove) JSON
// It contains either a list of students that take the subject
// If its `../remove/` - the students whom take the subjects will be return
// If its `../add/` - the student whom do not take the subjects will be return
[
    {
        "id": 1,
        "username": "slggs-2023-p1-001",
        "first_name": "Anarado",
        "last_name": "Kasimobi",
        "current_class": 1
    },
    // more students
[
```

### Other Resourceful Endpoints:

These resources require only authentication to access them, no permission is required.

* `/years/` - \[GET\] View a list of years with their individual `id` and `year_name` attributes.

* `/terms/` - \[GET\] View a list of terms with their individual `id` and `term_name` attributes.

* `/classes/` - \[GET\] View a list of classes with their individual `id` and `class_name` attributes.

* `/subjects/` - \[GET\] View a list of subjects with their individual `id` and `subject_name` attributes.


> The `id` and resource names `(year_name, term_name, class_name, subject_name)` is necessary for setting up and automating request parameters and form dropdown or select lists. You could use the id as the input form value and the names as the input or select texts. This way, once there are changes in the resource it will be updated on the fly. For example, a new subject gets added or its name modified or its subject ID. It will mostly be handy due to the resource IDs. Nevertheless, the values could be hardcoded into the form; a list of the resource IDs and their names could be found in the subsequent sections.

* A loop through the resource list will easily populate the form menu or select options. Sample View of the Resource Endpoints return value:

```json
// Years
[
    {
        "id": 1,
        "year_name": "2023"
    },
    // Subsequent years
]
```

```json
// Terms
[
    {
        "id": 1,
        "term_name": "First Term"
    },
    // Other terms
]
```

```json
// Classes
[
    {
        "id": 7,
        "class_name": "JS1"
    }
    // Other classes
]
```

```json
// Subjects
[
    {
        "id": 4,
        "subject_name": "Art"
    },
    // Other subjects
]
```

## Permissions:

* **Student:** can view his/her own record, nothing else.

* **Teacher:** can view his/her own record together with the classes and subjects he/she teaches. A teacher can also update students' result entries.

* **Dean:** can view deans, teachers, and students. A dean can also update a student result entry.

* A 403 Forbidden status code will be raised when a user tries to access resourcesz he/she isn't allowed to. Perhaps a student or teacher may try to pry around with the URLs. So, catch 403 exceptions and display a beautiful, "Permission Denied" message to those lurking around.


## General Notes:

* Most of the requests require the query parameters, pass them in. The ones that need them are specified above.

* Set up the form so that if no request parameter is passed, the defaults will be used.

* Request/Query Parameters: `BASE_URL/RESOURCE/?year_id=<int:year_id>&term_id=<int:term_id>&class_id=<int:class_id>`

* The defaults will be the current year, class and term.

* The student ID for the URL route will be gotten from the login auth return values.

* The default current year, class and term will also be gotten from the auth token return value.

* The Auth Return Value will Look Like this:


```json
// For Teachers
{
    "access": "jwt_access_token",
    "refresh": "jwt_refresh_token",
    "user": {
        "id": 35, // teacher id
        "username": "ella_brown@765",
        "first_name": "Ella",
        "last_name": "Brown",
        "classes": [ // Contains id's of classes taught by the teacher
            {
                "id": 1,
                "class_name": "Primary 1",
            }
            // more classes
        ],
        "subjects": [ // Contains id's of Subjects taught by the teacher
            {
                "id": 1,
                "subject_name": "English",
            },
            // more subjects
        ],
        "status": "teacher" // this tells you if a user is a teacher/student/dean/admin
    },
    "year_id": 1,
    "term_id": 1,
    "last_login": "Thursday, December 07, 2023 01:46 AM",
}
```

```json
// For Students
{
    "access": "jwt_access_token",
    "refresh": "jwt_refresh_token",
    "user": {
        "id": 13, // student id
        "username": "isabella_smith",
        "first_name": "Isabella",
        "last_name": "Smith",
        "current_class": 1, // tells you the student current class
        "status": "student" // this tells you if a user is a teacher/student/dean/admin
    },
    "year_id": 1,
    "term_id": 1,
    "last_login": "Thursday, December 07, 2023 01:46 AM",
}
```

* The bio status is present in all return values of every user, use it to determine who the current user is. The values are (student, teacher, dean, admin)

* For selecting some other value for year, class and term, a html select menu with the texts and the form having a value of the id will do the trick. So also for the list of subjects.

* For updating the report entries, the dictionary/object structure and key are strict. The update request Dictionary/ Json should be of this structure and keys:


```json
// Per Student and Multiple Subjects Update Request Data JSON Strucutre
{
    "subject_id_1": { "test_score": 40, "exam_score": 60}, // the dict key is the subject's IDs
    "subject_id_2": { "test_score": 13, "exam_score": 12}
    // more subjects as needed
}
```

```json
// Per Subject and Multiple Students Update Request Data JSON Strucutre
{
    "student_id_1": {"test_score": 12, "exam_score": 88.0}, // the dict key is the student's IDs
    "student_id_2": {"test_score": 40, "exam_score": 12},
    "student_id_3": {"test_score": 40, "exam_score": 13},
    // more students as needed
}
```

```json
// Teachers Termly Scoresheet JSON Structure
{
    "subject": {
        "id": 1,
        "subject_name": "Science"
    },
    "info": {
        "school": "Netrobase School",
        "year": "2023",
        "class": "P1",
        "term": "Annual"
    },
    "students_termly": [
        {
            "student_id": 1,
            "student_name": "John Doe",
            "termly_entry": {
                "subject_id": 12,
                "test_score": 5,
                "exam_score": 25,
                "total_score": 30,
                "grade": "F"
            }
        },
        {
            "student_id": 13,
            "student_name": "Isabella Smith",
            "termly_entry": {
                "subject_id": 1,
                "test_score": 35,
                "exam_score": 22,
                "total_score": 57,
                "grade": "C"
            },
        },
        // more students as available
    ]
}
```

```json
// Teacher Annual Scoresheet View [Not Editable]
{
    "subject": {
        "id": 1,
        "subject_name": "Science"
    },
    "info": {
        "school": "Netrobase School",
        "year": "2023",
        "class": "P1",
        "term": "Annual"
    },
    "students_annual": [
        {
            "student_id": 1,
            "student_name": "John Doe",
            "annual_entry": {
                "term_1": 45,
                "term_2": 0,
                "term_3": 0,
                "annual": 15.0,
                "grade": "F"
            }
        },
        {
            "student_id": 13,
            "student_name": "Isabella Smith",
            "annual_entry": {
                "term_1": 68,
                "term_2": 0,
                "term_3": 0,
                "annual": 22.7,
                "grade": "F"
            }
        }
    ]
}
```

```json
// Admin View of Teachers Credentials
[
    {
        "first_name": "Sophia",
        "last_name": "Williams",
        "username": "sophia_williams_019@nggs",
        "default_password": "sophia_williams_019@nggs"
    },
    // more teachers
]
```

```json
// Admin View of Students Credentials
[
    {
        "first_name": "John",
        "last_name": "Doe",
        "username": "nggs-2023-p1-001",
        "password": "0e15fc8f"
    },
    // more students
]
```

> The Editable Result Entry for a particular subject is just the `test_score` and `exam_score` fields. They also have a range constraint. Test Score Field can only take positive whole integers between `0 - 40`. While the Exam Score Field can only take a positive whole integer that is between `0 - 60`. This is validated on the application logic, a database validation constraint was put in place, and entries that fail the validation will just be **skipped**. So client-side validation will go a long way in ensuring the integrity of the entry values. Though it can be turned off by the client, a possible HTML form input field with `MAX` and `MIN` value constraints could be set up. To avoid skipping such entries. Also worth noting is that the result processing/computations are all handled on the application logic, you just need to make a request and get the processed data in JSON format returned.

> Also, Result Entry with wrong `subject_id` will be **skipped**.

* Here is a list of year, class, term and subjects IDS:


```json
YEAR_TEXT,ID
2023,1
2024,2
// We will start counting from this year (2023)
// Then as each year passes, we increment the year by 1
// And also increment the ID by 1
// the year been referred to here is an academic year
```

```json
TERM_TEXT,ID
First Term,1
Second Term,2
Third Term,3
Annual,4
```

```json
CLASS_TEXT,ID
P1,1
P2,2
P3,3
P4,4
P5,5
P6,6
JS1,7
JS2,8
JS3,9
SS1,10
SS2,11
SS3,12
```

```json
SUBJECT_TEXT,ID
English,1
Mathematics,2
Igbo,3
// will update the subjects soon
```

## Student Actions \[In Beta\]:

* Sample View of a Student Termly Result Return Dict:


```json
// Termly View
{
    "info": {
        "school": "My School", // the school name
        "year": "2023",
        "class": "P1",
        "term": "Annual"
    },
    "bio": {
        "id": 13,
        "username": "isabella_smith",
        "first_name": "Isabella",
        "last_name": "Smith",
        "current_class": 1
    },
    "termly_result": {
        "English": {
            "subject_id": 1,
            "test_score": 20,
            "exam_score": 20,
            "total_score": 40,
            "grade": "P"
        },
        "Geography": {
            "subject_id": 2,
            "test_score": 40,
            "exam_score": 23,
            "total_score": 63,
            "grade": "C"
        },
        // more subjects
    },
    "summary": {
        "total": 314,
        "position": 1,
        "no_in_class": 2
    }
}
```

* Sample View of a Student Annual Return Dict:


```json
// Annual View
{
    "info": {
        "school": "My School", // the school name
        "year": "2023",
        "class": "P1",
        "term": "Annual"
    },
    "bio": {
        "id": 13,
        "username": "isabella_smith",
        "first_name": "Isabella",
        "last_name": "Smith",
        "current_class": 1
    },
    "annual_result": {
        "English": {
            "term_1": 60,
            "term_2": 0,
            "term_3": 0,
            "annual": 60,
            "grade": "C"
        },
        "Geography": {
            "term_1": 32,
            "term_2": 0,
            "term_3": 0,
            "annual": 32,
            "grade": "F"
        },
        // more subjects
    },
    "summary": {
        "total": 314,
        "position": 1,
        "no_in_class": 2
    }
}
```

## Teacher Actions \[In Beta\]:

* IDs of classes and subjects a teacher teaches will be returned when he/she logs in, you can use that to determine the teachers' classes and subjects.

* After login, the teacher makes a choice of the subject he or she wants to edit by choosing the year, term, class and subject.

* The teacher will only see his/her class subjects based on the subjects and classes in the list of class and subject IDs.

* The current year and term will be selected by default based on the current year and term returned during authentication.

* After this choice, a call to this endpoint will get the scoresheets: \[GET\] `BASE_URL/teachers/scoresheets/<int:subject_id>/`

* Which could then be updated as needed with the same endpoint: \[PUT\] `BASE_URL/teachers/scoresheets/<int:subject_id>/`

* The year, term, class and subject will all be passed as request query parameters.

* A feature could be implemented in the UI for editable table boxes, that submits data entered on the fly after a minute or so, or it could be done through modal boxes.

* A Teacher can update multiple students' result entries for a particular subject in a year, class and term.

* A Teacher's Detail/Update JSON for both GET and PUT methods would look like this:


```json
// Sample View of teachers detail/update View
{
    "id": 19,
    "username": "sophia_williams@123",
    "first_name": "Sophia",
    "last_name": "Williams",
    "classes": [ // A list of class IDs that a teacher is associated with
        1,
        2
    ],
    "subjects": [ // A list of subject IDs that a teacher is associated with
        1,
        2,
        3,
    ]
}
```

## Dean Actions:

* A Dean can update a specific student's multiple subjects for a particular year, term and class and submit it all at a go, the student update endpoint can handle that.

* A Dean has access to all teacher's information and their scoresheets.


## Admin Actions:

* An admin has super (unrestricted) privilege for all CRUD operations and more.

> To add Student Result (and it's entries), simply update via the admin dashboard the CurrentTerm first (and only) instance. If the value is in the current term and the student has no result yet, just resave it.

> To transition students to new class or mark the student inactive after SS3, just update the CurrentYear first (and only) instance.


## SRMS Data Model Tenets:

To maintain the data integrity and ascertain the signals work as expected, these points ought to be followed. They are the facts/points/structure that govern the application logic, data model and its relationships.

* A year ought to be created first before any other resource.

* A year is made up of three terms. Every year has three terms.

* A class lasts through three terms and exists only within a year.

* Three are twelve possible classes. A student can be in all or some of them within a twelve-year interval.

* A student can be in only one class in a year.

* The highest class a student can be until the student account is rendered inactive automatically is class 12 (SS3).

* When a student account is rendered inactive either automatically or via the dashboard, new result won't be created for the student.

* A result is created any time there is a new term within a year and class.

* A result can have multiple entries. These entries are subjects the student is taking.

* An entry is tied to a particular result and a particular subject.

* A result is tied to a particular student, in a particular year, class and term.

* A year's result is collected together into an Annual.

* There can only be one annual in a year.

* *A result gets created any time there is a change in the* `CurrentYear` *or* `CurrentTerm` *model.*

* *A result could still be created via the Admin Dashboard Model Actions by selecting the students you want to create their results. This helps curtail database bulk overhead operations*

* *A result is also created by a teacher when the teacher tries to fetch a scoresheet for a year, term, subject and class. Results are fetched or created for all students who match the criterion. However, this is limited to students whose current* *class matches the class provided by the teacher and students who take the teacher's subject.*

* A result is associated with multiple entries, these entries are subjects and are created via signals but could still be created through the Admin Dashboard Model Action for Result.

* A student current\_class is promoted anytime there is a change in the `CurrentYear` model.

* A student inherits the subjects assigned to the student's current class.

* A student can have their instance specific subjects.

* This specific subject of a student can created via Entry by choosing the subject and result. The subject is added via the Subject model. Hence a result entry is tied to a particular result and subject.

* An entry is a subject taken by a student.

* An Annual Entry is a sum of the results entries (three terms) divided by the number of result `len(Annual.results.all())` associated with the Annual Result.

* Student credentials get updated when a new student instance is created and during class transitions.

* Student usernames are generated based on the `SCHOOL_NAME_ACRONYM-CURRENT_YEAR-CURRENT_CLASS-STUDENT_ID`

* Student Passwords are stored based on their hashed username, this is set as the default password. But when the student wants to log in, a password is set for the student based on the password pool. In cases of password reset, as a result of loss of password, the student password could be reset based on the hash of their username or a new password could be created for the student in the Password Pool.

* A student can have multiple passwords in the password pool, but the last one that is assigned to the student becomes the current working one. A student can't use the password used by another.

* This achieves a shared password pool model; hence, passwords are shared to student from the pool and a password from the pool gets tied to a student when used for the first time, such password can't be reused by another.

* When creating Students in the frontend/admin dashboard, just add any random username and password in the fields, the system will assign a unique username and password to the student before being saved, hence your random inputs will be overridden. This happens only during initial student creation. Another time it gets changed is during class promotions.

* A Student account is considered unique during creation from the management command by the first name, last name and current class fields. If there are students with the same names in the same class, one of their accounts will have to be created from the frontend/admin dashboard, after which the system will assign a unique username to the student, satisfying the student's account uniqueness.

* Same also Teachers, account uniqueness is based on their first name and last name.

* The teacher's username is generated based on the teacher's first name, last name, ID and school acronym. By default, the username is the same as the password, the teacher can then change the password to a more secure one.

* A teacher must be associated with at least one class/subject. So after initialization, the teacher has no class/subject. But afterward, the teacher can't remove all the classes or subjects he/she is associated with. If this implementation is not required/needed, one could easily call .clear() on the m2m when the request data is empty in the TeacherUpdateView. The admin could clear all teacher's subjects from the admin dashboard.

* Dean can view and update students at any time whether the student is active or not. However, a Teacher can only edit/view a student who is active in a particular year, class and term.

* ...will keep adding as the model undergoes changes :)


---