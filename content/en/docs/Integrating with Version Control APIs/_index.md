---
title: "Integrating with Version Control APIs"
linkTitle: "Integrating with Version Control APIs"
weight: 8
---

## Interacting with the GitLab API

### Subgroups

GitLab subgroups were introduced in GitLab 9.0, and are supported by DreamFactory. You will still have access to both repository lists, as well as the files and directories (and indeed branches) of a particular respository, however due to the nature of the GitLab API, our service creation process will slightly differ depending on what you wish to do.

#### Accessing the Repository List

To be able to access details of all repositories within your subgroup, proceed with creating your GitLab service in the usual manner(Services -> Source Control -> GitLab Service). However, for the `Namespace/Group` field, enter the _ID_ of the subgroup you wish to connect (instead of the name).

{{< alert color="success" title="Tip" >}}
If you're using the hosted version of GitLab, you can go to `https://gitlab.com/<groupname>/<subgroupname>` and the ID will be listed under the subgroup name.
If you're hosting yourself then you can make a GET request to `http://<gitserver>/api/v4/groups?search=<subgroupname>&private_token=<token>` to get the ID.
{{< /alert >}}

Your config tab will thus look similar to this:

<p>
    <img src="/images/version-control/gitlab-subgroup-with-id.png" />
</p>

Create your service, and assign a role and app in the usual manner (For more information on roles see [here](../generating-a-database-backed-api/#creating-a-role). For more about applications, see [here](../generating-a-database-backed-api/#creating-an-application)). To interact with the API, we will make a GET request to `../api/v2/<gitlabservicename>/_repo` 

{{< alert title="Reminder" >}}
Your application will generate an API Key for you. The header in your API call should be named `X-DreamFactory-Api-Key`. See more on API interaction [here](../generating-a-database-backed-api/#interacting-with-the-api)
{{</ alert >}}

You will get a response containing details of all the repositories within your subgroup in JSON format:

<p>
    <img src="/images/version-control/gitlab-get-repo.png" />
</p>

#### Accessing Individual Repositories

If you want to access one particular repository and its file structure, the process is much the same, but instead of giving the subgroup ID as the `Namespace/Group`, we need to give it `<groupName></subgroupName>`, i.e. our config tab will now look something like this:

<p>
    <img src="/images/version-control/gitlab-group-subgroup.png" />
</p>

Now we can make a GET request to `../api/v2/<gitlabservicename>/_repo/<repositoryname>`, and the JSON response will be the file structure of your repository.

<p>
    <img src="/images/version-control/gitlab-get-repo-reponame.png" />
</p>

We can also add a file path to the end of our URI to get further details about an individual file (such as commit ids). Using the above image as an example, a call to `../_repo/subgrouptest1/somefiles/testdesign.css` returns the following:

<p>
    <img src="/images/version-control/gitlab-get-file.png" />
</p>

Fantastic.

{{< alert title="Tip" color="success" >}}
If you want to see a different branch, you can add the parameter `?branch=<yourbranch>` to the end of the URI. This may also be useful when troubleshooting as Dreamfactory will default to searching for the `master` branch. If your repo was created in the GitLab dashboard, there is a good chance that the branch is `main`.
{{< /alert >}}