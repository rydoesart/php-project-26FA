# Milestone 2: Design Resource Library

## 1. Project Title

Design Resource Library

## 2. Introduction

Design Resource Library is a website where designers can save useful websites in personal collections called libraries. It is intended for design students, graphic designers, and web designers.

Users can create libraries for fonts, colour tools, web design references, and inspiration. Each saved resource has a name, link, optional description, and rating from 1 to 5.

Libraries can be public or private. Users can browse public libraries on the Discover page and upvote other users' collections.

## 3. GitHub Project Link

[View the project on GitHub](https://github.com/nic-dgl123-26FA-cvs1/php-project-26FA-rydoesart)

Instructor access needs to be checked before submission.

## 4. Database Design

The database will use MySQL and contain four tables. All tables will use InnoDB, and all ID fields will use `INT UNSIGNED`.

### users

Stores user accounts.

- **user_id:** INT UNSIGNED, primary key, auto-increment.
- **username:** VARCHAR(50), required and unique.
- **email:** VARCHAR(255), required and unique.
- **password_hash:** VARCHAR(255), required. Stores a hashed password.
- **created_at:** TIMESTAMP, required, defaults to the current date and time.

### libraries

Stores each user's collections.

- **library_id:** INT UNSIGNED, primary key, auto-increment.
- **user_id:** INT UNSIGNED, required, foreign key linked to users.user_id.
- **name:** VARCHAR(100), required.
- **description:** TEXT, optional.
- **visibility:** ENUM('public', 'private'), required, defaults to private.
- **created_at:** TIMESTAMP, required, defaults to the current date and time.

### resources

Stores the websites saved inside libraries.

- **resource_id:** INT UNSIGNED, primary key, auto-increment.
- **library_id:** INT UNSIGNED, required, foreign key linked to libraries.library_id.
- **name:** VARCHAR(150), required.
- **url:** VARCHAR(2048), required. Must be a valid HTTP or HTTPS link.
- **description:** TEXT, optional.
- **rating:** TINYINT UNSIGNED, required, with CHECK (rating BETWEEN 1 AND 5).
- **created_at:** TIMESTAMP, required, defaults to the current date and time.

### votes

Stores upvotes on public libraries.

- **user_id:** INT UNSIGNED, required, foreign key linked to users.user_id.
- **library_id:** INT UNSIGNED, required, foreign key linked to libraries.library_id.
- **created_at:** TIMESTAMP, required, defaults to the current date and time.

The user_id and library_id fields form one composite primary key. This means a user can vote for the same library only once. Vote totals are calculated by counting votes.

### Table relationships

- One user can own many libraries.
- One library can contain many resources.
- A user can upvote many public libraries, and a library can receive votes from many users.

All foreign keys use ON DELETE CASCADE. Deleting a library also deletes its resources and votes. Deleting an account deletes its libraries and their contents, plus any votes the user has made.

## 5. Data Flow

These steps describe how PHP and MySQL will work when the backend is built.

### Create a library

1. The logged-in user selects **Create Library**.
2. They enter a name, optional description, and public/private setting.
3. The form sends the information to PHP using POST.
4. PHP checks the user's session, form security token, and entered values.
5. PHP saves the library using a prepared statement and the owner's ID from the session.
6. The user is taken to the new library's details page.

### Add a resource

1. The user opens one of their libraries and selects **Add Resource**.
2. They enter a name, link, optional description, and rating.
3. The form sends the information to PHP using POST.
4. PHP checks that the user owns the library and validates the form security token and entered values.
5. PHP saves the resource using a prepared statement.
6. The user returns to the library details page and sees the new resource.

### Browse and upvote

1. A visitor or logged-in user opens **Discover**.
2. PHP retrieves public libraries and their vote totals. Libraries with no votes show zero.
3. The user opens a library to view its resources.
4. To upvote, the user must log in. After login, they return to the same library.
5. The upvote is sent using POST. PHP checks the session and form security token, confirms the library is public, and prevents users from voting for their own libraries.
6. PHP saves the vote and updates the displayed total.

If the user has already voted, the page shows **Already upvoted** without adding another vote or displaying a database error. Discover can sort libraries by the most upvotes.

### Basic validation and access rules

- Required names cannot be blank. Text must fit the database field limits.
- Resource links must use HTTP or HTTPS. Ratings must be whole numbers from 1 to 5.
- Invalid forms show an error and keep the user's entered values.
- Private libraries are visible only to their owners.
- Only owners can add, edit, or delete their libraries and resources. PHP checks ownership for every change.
- Changes use POST and a form security token to prevent unwanted submissions.
- Passwords are hashed. Database queries use prepared statements, and user text is escaped before appearing on a page.

## 6. Initial UI Design

The interface has not been built yet. The next step is an HTML/CSS prototype with sample data and a small amount of JavaScript. Login and saving will be labelled as demonstrations until the backend is working.

### Planned pages

- **Home:** Project introduction and links to Discover, Log In, and Sign Up.
- **Sign Up:** Username, email, password, and confirm password fields.
- **Log In:** Email and password fields.
- **My Libraries:** The user's libraries, with Create, View, Edit, and Delete controls.
- **Library Details:** Library information and its resources. Owners see management controls; other users can upvote public libraries.
- **Create/Edit Library:** Name, description, visibility, Save, and Cancel.
- **Add/Edit Resource:** Name, link, description, rating, Save, and Cancel.
- **Discover:** Public libraries with owner names, vote totals, and sorting by most upvotes.

### Layout

The pages will share a header with the project name and navigation links. Visitors will see Log In and Sign Up; logged-in users will see Log Out. Visitors opening My Libraries will be asked to log in.

Libraries will appear as cards. Cards will sit in columns on wider screens and stack on smaller screens. Forms will have clear labels, readable text, and visible keyboard focus.

The prototype will include an empty library, a validation error, public/private labels, and an upvoted state. Delete actions will ask for confirmation.

### Remaining work

- Build and connect the planned pages.
- Add sample libraries and resources.
- Demonstrate forms, sorting, login states, and upvotes.
- Check the layout on small and large screens and test keyboard navigation.

## 7. User Interaction Flow

### Account access

Home → Sign Up → Log In → My Libraries

Home → Log In → My Libraries

My Libraries → Log Out → Home

### Library management

My Libraries → Create Library → Save → Library Details

Library Details → Edit Library → Save → Library Details

Library Details → Delete Library → Confirm → My Libraries

### Resource management

Library Details → Add Resource → Save → Library Details

Library Details → Edit Resource → Save → Library Details

Library Details → Delete Resource → Confirm → Library Details

### Discover

Discover → Public Library Details → Resource Website

Discover → Sort by Upvotes → Sorted Libraries

Public Library Details → Upvote → Updated Vote Total

Public Library Details → Log In if Required → Return to Library → Upvote

Selecting **Cancel** returns to the previous page without saving. Cancelling a delete confirmation keeps the item. Users who have already voted see **Upvoted** and cannot vote again.
