#!/usr/bin/env python2.7

import json
import os
import requests

from optparse import OptionParser
from subprocess import Popen, PIPE

try:
    from spawn import get_editor_output
except ImportError:
    get_editor_output = None
    pass

GITHUB_PULL_URL = ("%s/repos/%s/pulls")


def get_curr_branch_name():
    p = Popen(["git", "rev-parse", "--abbrev-ref", "HEAD"], stdout=PIPE)
    curr_branch_name, _ = p.communicate()
    return curr_branch_name.strip()


def retrieve_config(filename):
    # gets home directory of user
    home = os.path.expanduser("~")
    path = os.path.join(home, filename)

    if not os.path.exists(path):
        return {}

    f = open(path, "r")
    config = json.load(f)
    f.close()

    return config


def create_pull_request(options, user, token, title,
                        github_url='https://api.github.com'):
    headers = {"Authorization": "token %s" % token,
            'Content-Type': 'application/json'}
    data = {
            "title": title,
            "base": options.base,
            "head": options.head
    }
    if options.body:
        data["body"] = options.body
    response = requests.post(GITHUB_PULL_URL % github_url, headers=headers,
                             data=json.dumps(data), verify=False)
    if response.status_code not in range(200, 299):
        raise Exception("Response %d: %s" % (response.status_code,
                                             response.content))
    return response.json()


def ensure_following_options(options, check_names):
    for name in check_names:
        if not getattr(options, name, False):
            raise Exception("You must provide a %s" % name)


def if_not_then_try_spawn(title):
    if title:
        return title
    if get_editor_output:
        title = get_editor_output(
                initial_message="Put pull request title here")
        return title
    else:
        raise Exception("You must provide a title")


if __name__ == "__main__":
    usage = "Usage: %prog [options]"
    parser = OptionParser(usage=usage)
    parser.add_option('-t', '--title', dest='title',
            help='title for pull request')
    parser.add_option('--body', dest='body',
            help='body for pull request')
    parser.add_option('-b', '--base', dest='base', default='master',
            help='base to diff against')
    parser.add_option('--head', dest='head',
            default=get_curr_branch_name(), help='head of feature branch')
    (options, args) = parser.parse_args()
    title = if_not_then_try_spawn(options.title)
    ensure_following_options(options, ["base", "head"])
    config = retrieve_config(".githubcredentials")
    token = config["token"]
    user = config["user"]
    github_url = config["github_url"]
    pull_body = create_pull_request(options, user, token, title, github_url)
    print pull_body["html_url"]
