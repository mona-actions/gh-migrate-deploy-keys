# gh-migrate-deploy-keys

This script is used by the `gh` command-line to help sync/migrate **GitHub Deploy Keys** of repositories from one Organization to another. This is usually needed when you migrate repositories from an original Organization into a new Organization.
If no input file is provided, it will find all **GitHub Deploy Keys** of all repositories found in the *source* Organization  and migrate them to the *destination* Organization.

If an input file is provided, it will only update the **GitHub Deploy Keys** of all repositories in the input file.

**Note:** You can **NOT** have a non unique **GitHub Deploy Key** in **GitHub**.
In order for this tool to work, it will need to copy the `public_key` and `title` of the **GitHub Deploy Key** to the **GitHub** repository. It will the **Delete** the **GitHub Deploy Key** from the **GitHub** repository. It will then **Create** a new **GitHub Deploy Key** with the same `title` and `public_key` in the source **GitHub** repository.

## PREREQS

You need to have the following to run this script successfully:
- **GitHub Personal Access Token** with a scope of "repos" and access to the organization(s) that will be updated.
- Either the name of the organizations to be synced/migrated, or a file containing the names of the repositories to be updated
- **jq** installed on the machine running the query
- [sem](https://www.gnu.org/software/parallel/) installable on ubuntu via `sudo apt-get install parallel`

## Input file

The `input file` should be in the format:
```csv
Org_Name/Repo_Name1,true
Org_Name/Repo_Name2,false
Org_Name/Repo_Name3
```

The format is: `Source_Org/Source_Repo,<Is the key READ only>`
If you set the `Is the key READ only` to `true`, it will make the key with `READ_ONLY`
If you set the `Is the key READ only` to `false`, it will make the key with `read/write`
**Note:** if not passed it will default to `true` for `READ_ONLY`

## Usage

`gh migrate-deploy-keys --source-org OriginalOrg --destination-org MyNewOrg --source-token ABCDEFG1234567 --destination-token 1234567ABCDEFG`

### Output

```bash
gh migrate-deploy-keys --destination-organization lukastestorg2 --destination-token <REDACTED> --source-organization lukastestorg1 --source-token <REDACTED>

######################################################
######################################################
########### GitHub Migrate Deploy keys ###############
######################################################
######################################################

NOTE:
This script is used by the gh command line to help migrate GitHub Deploy Keys
In order to do this, it will need to remove the deploy key from the
source organization repository and add it to the destination organization repository.
There is some level of risk associated with this script, so please use it with caution.

Logs will be written to:[/Users/admiralawkbar/github/gh-migrate-deploy-keys/gh-migrate-deploy-keys-1656617536.log]

----------------------------------------------------
Validating user input...
Validation of input successful
----------------------------------------------------
Getting all source repos for:[lukastestorg1]
Getting deploy keys for repo:[LukasTestOrg1/repo1]
Adding key:
 - ORG/REPO:[lukastestorg1/repo1]
 - KEY_ID:[PK_kwPOHig77M4EDmZS]
 - KEY_TITLE:[demokey1]
 - KEY_VALUE:[ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOvNXwEO4VumiiqDtyDUlYTLuIDpcNLDNupVxZqZt6x/]
 - KEY_READ_ONLY:[false]
Gathered all data from source GitHub Organization
----------------------------------------------------
Getting all destination repos for:[lukastestorg2]
Gathered all data from destination GitHub Organization
----------------------------------------------------
----------------------------------------------------
Parsing all data and generating migration list...
----------------------------------------------------
We found matching repo:[lukastestorg1/repo1] but did not find matching key:[demokey1] at:[lukastestorg2/repo1]
Adding to update list
----------------------------------------------------
Deleting key:[demokey1] from repo:[repo1]
----------------------------------------------------
Successfully deleted key:[demokey1] from repo:[repo1]
----------------------------------------------------
Adding key:[demokey1] to repo:[lukastestorg2/repo1]
KEY_VALUE:[ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOvNXwEO4VumiiqDtyDUlYTLuIDpcNLDNupVxZqZt6x/]
KEY_READ_ONLY:[false]
----------------------------------------------------
Successfully added key:[demokey1] to repo:[repo1]

######################################################
The script has completed
######################################################
Total Deploy Keys Created:[1]
Total Deploy Keys Deleted:[1]
```

The Output file will have contents like:

```txt
----------------------------------------------------
Deleting key:[demokey1] from repo:[lukastestorg1/repo1] with title:[demokey1]
KEY_ID:[68052562]
KEY_VALUE:[ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOvNXwEO4VumiiqDtyDUlYTLuIDpcNLDNupVxZqZt6x/]
Successfully deleted key:[demokey1] from repo:[repo1]
----------------------------------------------------
----------------------------------------------------
Creating key:[demokey1] from repo:[repo1] with title:[demokey1]
KEY_VALUE:[ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOvNXwEO4VumiiqDtyDUlYTLuIDpcNLDNupVxZqZt6x/]
KEY_READ_ONLY:[false]
Successfully added key:[demokey1] to repo:[repo1]
----------------------------------------------------
```

### Limitations

- The tool currently is only setup to work on `https://github.com`
- The tool currently scans all repositories in the source organization and destination organization using GraphQL. This could prove to use many of your API rate limit on *much larger* Organizations
