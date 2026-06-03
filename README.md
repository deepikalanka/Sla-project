# Sla project
<title>SLA Login</title>

<link href=
"https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css"
rel="stylesheet">

</head>
<body>

<div class="container mt-5">

<div class="card">

<div class="card-header">
Super Admin Login
</div>

<div class="card-body">

<form method="post">

<div class="mb-3">
<label>User Name</label>
<input
class="form-control"
name="username">
</div>

<div class="mb-3">
<label>Password</label>
<input
type="password"
class="form-control"
name="password">
</div>

<button
name="login"
class="btn btn-primary">
Login
</button>

</form>

</div>
</div>

</div>
</body>
</html>

<?php
exit;
}
?>

<!DOCTYPE html>
<html>

<head>

<title>SLA Configuration</title>

<link href=
"https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css"
rel="stylesheet">

</head>

<body>

<div class="container mt-4">

<div class="d-flex justify-content-between">

<h2>SLA Configuration</h2>

<a href="?logout=1"
class="btn btn-danger">
Logout
</a>

</div>

<hr>

<!-- MENU 1 -->

<div class="card mb-4">

<div class="card-header">
Menu 1 - User/Admin Creation
</div>

<div class="card-body">

<form method="post">

<div class="row">

<div class="col-md-3">
<input
name="username"
class="form-control"
placeholder="Username">
</div>

<div class="col-md-3">
<input
name="email"
class="form-control"
placeholder="Email">
</div>

<div class="col-md-2">
<select
name="role"
class="form-select">

<option value="USER">
USER
</option>

<option value="ADMIN">
ADMIN
</option>

</select>
</div>

<div class="col-md-2">

<select
name="level_id"
class="form-select">

<option value="">
Select Level
</option>

<?php
foreach(
$pdo->query("SELECT * FROM levels")
as $level)
{
?>

<option value="<?= $level['id'] ?>">
<?= $level['level_name'] ?>
</option>

<?php
}
?>

</select>

</div>

<div class="col-md-2">

<button
name="saveUser"
class="btn btn-success">

Save

</button>

</div>

</div>

</form>

</div>
</div>

<!-- MENU 2 -->

<div class="card mb-4">

<div class="card-header">
Menu 2 - Level Flexibility
</div>

<div class="card-body">

<form method="post">

<div class="row">

<div class="col-md-6">

<input
name="level_name"
class="form-control"
placeholder="Level Name">

</div>

<div class="col-md-3">

<button
name="addLevel"
class="btn btn-primary">

Add Level

</button>

</div>

</div>

</form>

<hr>

<table class="table">

<tr>
<th>ID</th>
<th>Level</th>
<th>Action</th>
</tr>

<?php
foreach(
$pdo->query("SELECT * FROM levels")
as $row)
{
?>

<tr>

<td><?= $row['id'] ?></td>

<td><?= $row['level_name'] ?></td>

<td>

<a
class="btn btn-danger btn-sm"
href="?deleteLevel=<?= $row['id'] ?>">

Delete

</a>

</td>

</tr>

<?php
}
?>

</table>

</div>

</div>

<!-- MENU 3 -->

<div class="card">

<div class="card-header">
Menu 3 - Reasons & Times
</div>

<div class="card-body">

<form method="post">

<div class="row">

<div class="col-md-6">

<input
name="reason_name"
class="form-control"
placeholder="Reason">

</div>

<div class="col-md-3">

<button
name="addReason"
class="btn btn-primary">

Add Reason

</button>

</div>

</div>

</form>

<hr>

<form method="post">

<div class="row">

<div class="col-md-5">

<select
name="reason_id"
class="form-select">

<?php
foreach(
$pdo->query("SELECT * FROM reasons")
as $reason)
{
?>

<option
value="<?= $reason['id'] ?>">

<?= $reason['reason_name'] ?>

</option>

<?php
}
?>

</select>

</div>

<div class="col-md-3">

<input
type="number"
name="duration_hours"
class="form-control"
placeholder="Hours">

</div>

<div class="col-md-2">

<button
name="saveSLA"
class="btn btn-success">

Save SLA

</button>

</div>

</div>

</form>

<hr>

<table class="table">

<tr>
<th>Reason</th>
<th>Hours</th>
</tr>

<?php

$sql="
SELECT
r.reason_name,
s.duration_hours
FROM sla_times s
JOIN reasons r
ON r.id=s.reason_id
";

foreach(
$pdo->query($sql)
as $row)
{
?>

<tr>

<td>
<?= $row['reason_name'] ?>
</td>

<td>
<?= $row['duration_hours'] ?>
</td>

</tr>

<?php
}
?>

</table>

</div>

</div>

</div>

</body>
</html>
# create_user.php

## Purpose

This file is responsible for creating Users and Admins in the SLA Configuration System.

### Features

* Creates normal users.
* Creates admin users.
* Assigns an admin level during admin creation.
* Encrypts passwords using PHP's `password_hash()`.
* Stores user information in the database.

### PHP Code

```php
<?php

// Database Connection
include 'config.php';

// Get form data
$name = $_POST['name'];
$email = $_POST['email'];
$contact = $_POST['contact'];

// Encrypt password before storing
$password = password_hash(
    $_POST['password'],
    PASSWORD_DEFAULT
);

// User Role
// Possible values:
// USER
// ADMIN
$role = $_POST['role'];

// Admin Level
// Required only for ADMIN role
$level_id = !empty($_POST['level_id'])
    ? $_POST['level_id']
    : NULL;

// User Status
// Active / Inactive
$status = $_POST['status'];

// Insert user/admin record
$sql = "INSERT INTO users
(
    name,
    email,
    contact_number,
    password,
    role,
    level_id,
    status
)
VALUES
(
    ?, ?, ?, ?, ?, ?, ?
)";

$stmt = $conn->prepare($sql);

$stmt->bind_param(
    "sssssis",
    $name,
    $email,
    $contact,
    $password,
    $role,
    $level_id,
    $status
);

// Execute Query
if ($stmt->execute()) {

    echo "User/Admin Created Successfully";

} else {

    echo "Error : " . $stmt->error;

}

?>
```

---

## Input Parameters

| Parameter | Description          |
| --------- | -------------------- |
| name      | User/Admin Name      |
| email     | Unique Email Address |
| contact   | Contact Number       |
| password  | Login Password       |
| role      | USER or ADMIN        |
| level_id  | Admin Level ID       |
| status    | Active / Inactive    |

---

## Example Request

### Create User

```text
Name: tony
Email: tony@example.com
Role: USER
Status: Active
```

### Create Admin

```text
Name: sandeep
Email: sandeep@example.com
Role: ADMIN
Level: Level 2
Status: Active
```

---

## Expected Output

```text
User/Admin Created Successfully
```

---

## Related SLA Module

Menu → User/Admin Creation

* Create User
* Create Admin
* Assign Admin Level
* Store User Details

```
```
# add_level.php

## Purpose

This file is responsible for creating new Admin Levels in the SLA Configuration System.

### Features

* Creates new admin levels dynamically.
* Stores level name and description in the database.
* Supports flexible role management.
* Used by System Admin for level configuration.

### PHP Code

```php
<?p
hp

// Database Connection
include 'config.php';

// Get form data
$level_name = $_POST['level_name'];
$description = $_POST['description'];

// Insert Admin Level
$sql = "INSERT INTO admin_levels
(
    level_name,
    description
)
VALUES
(
    ?, ?
)";

$stmt = $conn->prepare($sql);
# update_sla_time.php

## Purpose

This file is responsible for updating the SLA Duration assigned to a specific Reason in the SLA Configuration System.

### Features

* Updates SLA time for an existing reason.
* Allows System Admin to modify SLA durations.
* Changes are applied to future requests.
* Maintains centralized SLA management.

### PHP Code

```php
<?php

// Database Connection
include 'config.php';

// Get Form Data
$id = $_POST['id'];
$sla_time = $_POST['sla_time'];

// Update SLA Duration
$sql = "UPDATE reasons
SET sla_time = ?
WHERE id = ?";

$stmt = $conn->prepare($sql);

$stmt->bind_param(
    "ii",
    $sla_time,
    $id
);

// Execute Query
if ($stmt->execute()) {

    echo "SLA Updated Successfully";

} else {

    echo "Error : " . $stmt->error;

}

?>
```

---

## Input Parameters

| Parameter | Description      |
| --------- | ---------------- |
| id        | Unique Reason ID |
| sla_time  | New SLA Duration |

---

## Example Request

### Update Password Reset SLA

```text
Reason ID : 1
Current SLA : 1 Hour
New SLA : 2 Hours
```

### Update Access Request SLA

```text
Reason ID : 2
Current SLA : 2 Hours
New SLA : 4 Hours
```

---

## Database Table

### reasons

| Column Name | Description          |
| ----------- | -------------------- |
| id          | Unique Reason ID     |
| reason_name | Reason Name          |
| sla_time    | SLA Duration         |
| time_unit   | Minutes / Hours      |
| created_at  | Record Creation Date |

---

## Expected Output

```text
SLA Updated Successfully
```

---

## Related SLA Module

Menu → Reasons & Times

* View Reasons
* Add Reason
* Edit Reason
* Delete Reason
* Update SLA Time

---

## Business Rules

1. SLA Duration must be greater than zero.
2. Only System Admin can update SLA configurations.
3. Changes should be reflected immediately.
4. Existing reason records remain unchanged except for the SLA duration.
5. Updated SLA values apply to future requests.

---

## Workflow

```text
System Admin Login
        ↓
Reasons & Times
        ↓
Select Reason
        ↓
Modify SLA Duration
        ↓
Save Changes
        ↓
SLA Updated Successfully
```

$stmt->bind_param(
    "ss",
    $level_name,
    $description
);

// Execute Query
if ($stmt->execute()) {

    echo "Level Added Successfully";

} else {

    echo "Error : " . $stmt->error;

}

?>
```

---

## Input Parameters

| Parameter   | Description              |
| ----------- | ------------------------ |
| level_name  | Name of the Admin Level  |
| description | Description of the Level |

---# Project Structure

The SLA Management System is organized into multiple modules to separate configuration, user management, level management, SLA configuration, and database files.

```text
sla-management/
│
├── config/
│   └── config.php
│
├── users/
│   ├── create_user.php
│   ├── edit_user.php
│   └── list_users.php
│
├── levels/
│   ├── add_level.php
│   ├── edit_level.php
│   ├── assign_level.php
│   └── list_levels.php
│
├── reasons/
│   ├── add_reason.php
│   ├── edit_reason.php
│   ├── delete_reason.php
│   └── list_reasons.php
│
├── login/
│   └── login.php
│
└── database/
    └── sla_management.sql
```

---

## Folder Description

### config/

Contains application configuration files.

| File       | Description                                                        |
| ---------- | ------------------------------------------------------------------ |
| config.php | Database connection configuration used throughout the application. |

---

### users/

Responsible for User and Admin management.

| File            | Description                    |
| --------------- | ------------------------------ |
| create_user.php | Create new Users and Admins.   |
| edit_user.php   | Update user/admin information. |
| list_users.php  | Display all users and admins.  |

---

### levels/

Responsible for Admin Level management.

| File             | Description                          |
| ---------------- | ------------------------------------ |
| add_level.php    | Create new admin levels.             |
| edit_level.php   | Modify existing admin levels.        |
| assign_level.php | Assign or reassign levels to admins. |
| list_levels.php  | Display available admin levels.      |

---

### reasons/

Responsible for SLA configuration.

| File              | Description                                |
| ----------------- | ------------------------------------------ |
| add_reason.php    | Create SLA reasons and assign time limits. |
| edit_reason.php   | Modify SLA reasons.                        |
| delete_reason.php | Remove SLA reasons.                        |
| list_reasons.php  | Display configured SLA reasons.            |

---

### login/

Responsible for authentication.

| File      | Description                                     |
| --------- | ----------------------------------------------- |
| login.php | Handles Super Admin/System Admin login process. |

---

### database/

Contains database scripts.

| File               | Description                                                  |
| ------------------ | ------------------------------------------------------------ |
| sla_management.sql | Database schema including users, levels, and reasons tables. |

---

## Module Mapping

### Menu 1: User/Admin Creation

Files Used:

* create_user.php
* edit_user.php
* list_users.php

Features:

* Create Users
* Create Admins
* Assign Levels during Admin Creation

---

### Menu 2: Levels Flexibility

Files Used:

* add_level.php
* edit_level.php
* assign_level.php
* list_levels.php

Features:

* View Levels
* Add Levels
* Edit Levels
* Reassign Levels

---

### Menu 3: Reasons & Times

Files Used:

* add_reason.php
* edit_reason.php
* delete_reason.php
* list_reasons.php

Features:

* Add Reasons
* Edit Reasons
* Delete Reasons
* Configure SLA Duration

---

## Technology Stack

* PHP
* MySQL
* HTML
* CSS
* Bootstrap
* JavaScript

---

## User Roles

### Super Admin

* Access SLA Configuration
* Create Users
* Create Admins
* Assign Admin Levels

### System Admin

* Manage Levels
* Manage Reasons
* Configure SLA Times

### User

* Access assigned application features
* No configuration permissions

```
```


## Example Request

### Add Level

```text
Level Name : Level 1
Description : First Level Approval Team
```

### Add Escalation Level

```text
Level Name : Escalation Manager
Description : Handles escalated tickets and approvals
```

---

## Database Table

### admin_levels

| Column      | Description          |
| ----------- | -------------------- |
| id          | Unique Level ID      |
| level_name  | Admin Level Name     |
| description | Level Description    |
| created_at  | Record Creation Time |

---

## Expected Output

```text
Level Added Successfully
```

---

## Related SLA Module

Menu → Levels Flexibility

* View Levels
* Add Level
* Edit Level
* Delete Level
* Assign Level
* Reassign Level

---

## Business Rules

1. Level Name should be unique.
2. Only System Admin can create levels.
3. Level changes should be reflected immediately.
4. Newly created levels must be available for Admin assignment.

```
```
# update_admin_level.php

## Purpose

This file is responsible for updating or reassigning an Admin's Level in the SLA Configuration System.

### Features

* Reassigns an existing Admin to a different level.
* Updates the level assignment in the database.
* Supports dynamic level management.
* Changes take effect immediately after update.

### PHP Code

```php
<?php

// Database Connection
include 'config.php';

// Get Form Data
$user_id = $_POST['user_id'];
$new_level = $_POST['level_id'];

// Update Admin Level
$sql = "UPDATE users
SET level_id = ?
WHERE id = ?
AND role = 'ADMIN'";

$stmt = $conn->prepare($sql);

$stmt->bind_param(
    "ii",
    $new_level,
    $user_id
);

// Execute Query
if ($stmt->execute()) {

    echo "Admin Level Updated";

} else {

    echo "Error : " . $stmt->error;

}

?>
```

---

## Input Parameters

| Parameter | Description            |
| --------- | ---------------------- |
| user_id   | Unique ID of the Admin |
| level_id  | New Admin Level ID     |

---

## Example Request

### Reassign Admin Level

```text
Admin ID : 5
Current Level : Level 1
New Level : Level 2
```

### Escalation Example

```text
Admin ID : 8
Current Level : Level 2
New Level : Escalation Manager
```

---

## Database Table

### users

| Column   | Description          |
| -------- | -------------------- |
| id       | User/Admin ID        |
| role     | USER / ADMIN         |
| level_id | Assigned Admin Level |
| status   | Active / Inactive    |

---

## Expected Output

```text
Admin Level Updated
```

---

## Related SLA Module

Menu → Levels Flexibility

* View Levels
* Add Level
* Edit Level
* Assign Level
* Reassign Level

---

## Business Rules

1. Only users with role = ADMIN can be reassigned.
2. The selected level must exist in the Admin Levels table.
3. Changes should be reflected immediately.
4. System Admin is authorized to perform level reassignment.

---

## Workflow

```text
System Admin Login
        ↓
Levels Management
        ↓
Select Admin
        ↓
Choose New Level
        ↓
Save Changes
        ↓
Admin Level Updated
```
# add_reason.php

## Purpose

This file is responsible for adding new SLA Reasons and their corresponding SLA Time configurations in the SLA Configuration System.

### Features

* Creates new SLA reasons.
* Assigns SLA duration to each reason.
* Supports configurable time units (Minutes/Hours).
* Stores SLA configuration in the database.
* Used by System Admin to manage SLA settings.

### PHP Code

```php
<?php

// Database Connection
include 'config.php';

// Get Form Data
$reason_name = $_POST['reason_name'];
$sla_time = $_POST['sla_time'];
$time_unit = $_POST['time_unit'];

// Insert Reason and SLA Time
$sql = "INSERT INTO reasons
(
    reason_name,
    sla_time,
    time_unit
)
VALUES
(
    ?, ?, ?
)";

$stmt = $conn->prepare($sql);

$stmt->bind_param(
    "sis",
    $reason_name,
    $sla_time,
    $time_unit
);

// Execute Query
if ($stmt->execute()) {

    echo "Reason Added Successfully";

} else {

    echo "Error : " . $stmt->error;

}

?>
```

---

## Input Parameters

| Parameter   | Description               |
| ----------- | ------------------------- |
| reason_name | Name of the SLA Reason    |
| sla_time    | SLA Duration Value        |
| time_unit   | Time Unit (Minutes/Hours) |

---

## Example Request

### Example 1

```text
Reason Name : Password Reset
SLA Time    : 1
Time Unit   : Hours
```

### Example 2

```text
Reason Name : Access Request
SLA Time    : 2
Time Unit   : Hours
```

### Example 3

```text
Reason Name : Software Installation
SLA Time    : 4
Time Unit   : Hours
```

---

## Database Table

### reasons

| Column Name | Description          |
| ----------- | -------------------- |
| id          | Unique Reason ID     |
| reason_name | Reason Name          |
| sla_time    | SLA Duration         |
| time_unit   | Minutes or Hours     |
| created_at  | Record Creation Date |

---

## Expected Output

```text
Reason Added Successfully
```

---

## Related SLA Module

Menu → Reasons & Times

* Add Reason
* Edit Reason
* Delete Reason
* View Reasons
* Configure SLA Time

---

## Business Rules

1. Reason Name is mandatory.
2. SLA Time must be greater than 0.
3. Time Unit must be either Minutes or Hours.
4. Only System Admin can create or modify SLA configurations.
5. Newly added reasons become available immediately in the system.

---

## Workflow

```text
System Admin Login
        ↓
Reasons & Times
        ↓
Add New Reason
        ↓
Enter SLA Duration
        ↓
Save Configuration
        ↓
Reason Added Successfully
```
