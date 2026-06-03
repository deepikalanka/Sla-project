# Sla-project
<?php
session_start();

/*****************************************
 DATABASE CONNECTION
******************************************/
$pdo = new PDO(
    "mysql:host=localhost;dbname=sla_demo",
    "root",
    ""
);

$pdo->setAttribute(
    PDO::ATTR_ERRMODE,
    PDO::ERRMODE_EXCEPTION
);

/*****************************************
 LOGIN
******************************************/
if(isset($_POST['login']))
{
    if(
        $_POST['username']=='admin'
        &&
        $_POST['password']=='admin123'
    )
    {
        $_SESSION['admin']=true;
    }
}

/*****************************************
 LOGOUT
******************************************/
if(isset($_GET['logout']))
{
    session_destroy();
    header("Location:index.php");
    exit;
}

/*****************************************
 USER / ADMIN CREATE
******************************************/
if(isset($_POST['saveUser']))
{
    $stmt = $pdo->prepare(
        "INSERT INTO users
        (
            username,
            email,
            role,
            level_id
        )
        VALUES
        (
            ?,
            ?,
            ?,
            ?
        )"
    );

    $levelId =
        $_POST['role']=="ADMIN"
        ? $_POST['level_id']
        : null;

    $stmt->execute([
        $_POST['username'],
        $_POST['email'],
        $_POST['role'],
        $levelId
    ]);
}

/*****************************************
 ADD LEVEL
******************************************/
if(isset($_POST['addLevel']))
{
    $stmt=$pdo->prepare(
        "INSERT INTO levels(level_name)
         VALUES(?)"
    );

    $stmt->execute([
        $_POST['level_name']
    ]);
}

/*****************************************
 DELETE LEVEL
******************************************/
if(isset($_GET['deleteLevel']))
{
    $stmt=$pdo->prepare(
        "DELETE FROM levels WHERE id=?"
    );

    $stmt->execute([
        $_GET['deleteLevel']
    ]);
}

/*****************************************
 ADD REASON
******************************************/
if(isset($_POST['addReason']))
{
    $stmt=$pdo->prepare(
        "INSERT INTO reasons(reason_name)
         VALUES(?)"
    );

    $stmt->execute([
        $_POST['reason_name']
    ]);
}

/*****************************************
 SAVE SLA TIME
******************************************/
if(isset($_POST['saveSLA']))
{
    $stmt=$pdo->prepare(
        "INSERT INTO sla_times
        (
            reason_id,
            duration_hours
        )
        VALUES
        (
            ?,
            ?
        )"
    );

    $stmt->execute([
        $_POST['reason_id'],
        $_POST['duration_hours']
    ]);
}

if(!isset($_SESSION['admin']))
{
?>
<!DOCTYPE html>
<html>
<head>
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
