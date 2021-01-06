# labelset
Just a node tools to paste in the browser console to create and update label my label on each of my repositories.

## Usage

Use this tools to update the labels without any deps on the repo (You just need write access) : https://github.com/Financial-Times/github-label-sync

Download the yaml files that describe the labels in [`./labels.yaml`](https://github.com/shiipou/labelset/edit/master/labels.yaml)

Install is with this :
```bash
npm install -g github-label-sync
```

And apply labels with the following :
```bash
github-label-sync --access-token <GITHUB_ACCESS_TOKEN> --labels ./labels.yml <GITHUB_ORG_NAME>/<GITHUB_REPO_NAME>
```
