# js.tensorflow.org

This repo is for the website for TensorFlow.js. The API docs are built using Hexo (a static site generator) that puts static assets in to the `public` folder. 
The rest of the webstie is written as markdown and built internally within Google.

## Development Setup

You need node.js, yarn, and git to use this repo effectively. Note that it clones the tfjs repo (as a git submodules) in order to build API docs.

After checking out this repo run

```
yarn prep
```

This will download the two git submodules (or if you have already downloaded them before **will pull down changes from master**) and then install the project dependencies. 

To start a dev server for the site. You should be able to make changes to the site and see them reflected in the dev server

When you pull new commits from git you should run `yarn prep` again.

## Building and previewing the site

```
yarn build // note that you may need to build the API before running this (see below for details)
```

```
yarn serve
```

Doing a full build for final deployment can be done using (see below for more details)

```
yarn build-prod
```

## Making Changes

There are two broad classes of changes you might want to make, site content/design and API documentation changes.

### Site Content/Design

All the site content except for the API docs are stored in the `docs` folder as markdown and yaml files. These files are served from tensorflow.org/js

You can submit a change by editing those files and can preview your changes in any markdown preview tool or using the GitHub pull review interface. These files are built using internal google tools so can't be previewed along with the site design outside of Google.

### API Documentation

Updating the API docs is a bit more involved as they are built from the sources of tfjs-core, tfjs-layers, tfjs-node, tfjs-vis etc. The template for api docs is `api.hbs` and each version of the docs has a corresponding folder in `_data`. Files in `_data` are automatically generated and shouldn't be edited.

To edit the docs and see changes reflected in the site you can edit the repository that is located in `libs`. Note that this submodule behaves like *regular a git repository* and has an origin pointing to the canonical repository for `tfjs`. Making an API doc change involves making a commit to the tfjs repo and to this one. You may need to add a new git remote in the submodule if you want to make a PR from a fork of tfjs-website repo. Make sure to do `git checkout master` or make a new branch for your changes.

There are two ways to regenerate the docs json.

During local development (e.g. if you have changes in libs), run:

```
yarn [build-api|build-vis-api|build-node-api] // depending on which api docs you want to regenerate.
```

and 

```
yarn serve
```

This will build a version of the api known as `local`. It will be accessible at `http://localhost:8080/api/local/`. This content is not checked in, nor is it linked to
anywhere on the site, but it is suitable for testing changes to docs.


Once the your changes have been accepted into the repo in question (either tfjs-core or tfjs-layers), you can run `yarn prep` again in this repo (tfjs-website) to sync everything up (pull the most recent commits from master for each). You can then commit the **new submodule info** in the tfjs-website repo.

Once you are done you can do a full production build of the site using:

```
yarn build-prod
```

Note that this command will do a number of things that will **modify your working tree**. Its purpose is to build a new production build suitable for deployment to the site, as such it only builds docs that are in a released version of tfjs-layers and tfjs-core. The version it builds is driven off of the `@tensorflow/tfjs` dependency in package.json. **Build prod cannot build your local doc changes into the site**. To do this it will do a checkout of `libs/tfjs-core` and `libs/tfjs-layers` that correspond to the dependencies listed for `@tensorflow/tfjs`. This will modify your working tree (in libs).

In both these cases starting the dev server and refreshing the page should allow you to see changes.

In addition to `http://localhost:8080/api/local/`, the build also provides `http://localhost:8080/api/latest/` which points to the last **production** version of the docs that have been built. `latest` will never point to `local`

### Other API pages

These are the API pages generated by the project

- http://localhost:8080/api/ : for tfjs (union package)
- http://localhost:8080/api_vis/ : for tfjs-vis

### Deployment

To build the site run

```
yarn build-prod
```

Deployment instructions are available internally.
