# gh-migrate-deploy-keys

This script is used by the `gh` command-line to help sync/migrate **GitHub Deploy Keys** of repositories from one Organization to another. This is usually needed when you migrate repositories from an original Organization into a new Organization. If no input file is provided, it will find all **GitHub Deploy Keys** of all repositories found in the *source* Organization  and migrate them to the *destination* Organization.

If an input file is provided, it will only update the **GitHub Deploy Keys** of all repositories in the input file.

**Note:** You can **NOT** have a non unique **GitHub Deploy Key** in **GitHub**.
In order for this tool to work, it will need to copy the `public_key` and `title` of the **GitHub Deploy Key** to the **GitHub** repository. It will the **Delete** the **GitHub Deploy Key** from the **GitHub** repository. It will then **Create** a new **GitHub Deploy Key** with the same `title` and `public_key` in the source **GitHub** repository. 

## PREREQS

You need to have the following to run this script successfully:
- **GitHub Personal Access Token** with a scope of "repos" and access to the organization(s) that will be updated.
- Either the name of the organizations to be synced/migrated, or a file containing the names of the repositories to be updated
- **jq** installed on the machine running the query

## Input file

The `input file` should be in the format:
```csv
Repo_Name1
Repo_Name2
Repo_Name3
```

## Usage

`gh migrate-deploy-keys --source-org OriginalOrg --destination-org MyNewOrg --source-token ABCDEFG1234567 --destination-token 1234567ABCDEFG`

### Output

```bash
gh  migrate-deploy-keys -do lukastestorg2 -dt <REDACTED> -so lukastestorg1 -st <REDACTED>

```

### Limitations

- The tool currently is only setup to work on `https://github.com`
- The tool currently scans all repositories in the source organization and destination organization using GraphQL. This could prove to use many of your API rate limit on *much larger* Organizations