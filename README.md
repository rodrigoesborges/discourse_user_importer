# Discourse User Importer
This script will create users for a Discourse installation given a CSV file with
users.



> 🚨 **Deprecation Warning** 🚨
>
> Please be aware that this script is quite old and Discourse has evolved since then. If you want to add users to your Discourse installation automatically, please check out the following options:
>
> There are a few different programs that help automate the process using the API. For example:
> * https://github.com/pfaffman/discourse-user-creator
> * https://github.com/safisher/discourse_create_user
>
> As well as various helpful threads on the Discourse forum:
> * https://meta.discourse.org/t/creating-user-via-api/36133
> * https://meta.discourse.org/t/creating-active-users-via-the-api-gem/33133/36
> * https://meta.discourse.org/t/creating-a-user-through-restful-users-api/9359
>
> Thanks to [@safisher](https://github.com/safisher) for these tips

Some features include:

* Password is automatically generated, but your users need to reset it
* Imported users are automatically approved by the system user (`id: -1`)
* Imported users are automatically granted `trust_level: 1` permissions, just like invited users
* Users can be automatically assigned to one or more predefined groups
* If a user already exists, but has new groups in the CSV-file, these groups will be automatically added to the existing user
* Special task to check if usernames already exist

Caveats:

* Error handling may be a little rough. Please check beforehand if all data in
  your CSV-file is correct, e.g. that the groups you want to use already exist.

## Import file format

    email;name;username;title;groups

Field      | Required | Description
-----------|----------|------------
`email`    | **Yes**  | The email address of the user
`name`     | **Yes**  | The user's full name
`username` | No       | The user's user name. If none is supplied, this will be generated from the email address.
`title`    | No       | The user's title, e.g. _Vice President of Engineering_
`groups`   | No       | A comma separated list of groups a user should be added to. When left empty, the user will not be added to any specific groups.

## On passwords
For each user a random password is generated using `SecureRandom.hex`. Users
are able to set their own password after importing by using the password reset
function in Discourse.

## Running it
Add this file to your `lib/tasks` and run one of the following tasks:

To check if one or more usernames already exists, without importing:

    $ rake user_importer:check[/path/to/users.csv]

To import all the users in the file:

    $ rake user_importer:import[/path/to/users.csv]

## After running this script
Since users are automatically approved, they can log in right away after running
this script. Because the user's password is an automatically generated random
string, a newly imported user can only log in by generating a new password
through the password reset function in Discourse. They will then receive a link
to set a new password in their email.
