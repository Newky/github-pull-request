Getting a Github Access token
-----------------------------

I tested this on github enterprise, so steps may vary slightly with the github api.
NOTE: \<url\> listed below should be changed to relevant github api url, that's either api.github.com for GitHub and yourdomain.com/api/v3 for enterprise.

curl -k -u '\<github-username\>:\<github-password\>' -d '{"scopes":["repo", "user"],"note":"test?"}' https://\<url\>/authorizations

This will return a json blob, where one of the keys will be token.

Installation
------------

- Put the script in the /usr/bin
- Create a file called .githubcredentials in your HOME directory and place the following in it


{
	"user": \<your github name\>,
	"token": \<your github token\>,
	"github_url": \<the github url of the github instance you are using\> 
}


You will now have the following command available:

git pull\_request

It takes a number of commandline arguments:
- title (--title) (Mandatory) -> The title of the pull request
- Body (--body) (Optional) -> The body of the pull request
- Base (--base) (Optional) -> The base commit to diff against (defaults to master)
- Head (--head) (Optional) -> The head commit to diff against (defaults to current branch, beware that this branch must be pushed remotely first.)
