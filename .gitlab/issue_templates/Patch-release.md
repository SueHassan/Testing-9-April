- [ ] Schedule the patch release by setting a **Due date**

- [ ] Create preparation MRs by following the [instructions in release-tools](https://gitlab.com/gitlab-org/release-tools/blob/master/doc/picking-into-merge-requests.md).

- [ ] Cherry-pick changes into preparation MRs following their instructions

- [ ] Cherry-pick remaining merge requests labeled <%= version.picking_label %> using this [Merged MRs list]

- [ ] Check the following list of critical issues/MRs which are to be included in `<%= version.to_patch %>`. Where appropriate, ensure each has made it into both CE and EE
  
  - [ ] REFERENCE_TO_MR_TO_PICK

- [ ] Make sure the **Due date** is up-to-date

- [ ] If you didn't merge CE into EE in the preparation branches, or if the CE stable branch had additional commits, merge CE `<%= version.stable_branch %>` into EE `<%= version.stable_branch(ee: true) %>` following the [Merging a CE stable branch into its EE counterpart] guide. (In general this should be unnecessary.)

- [ ] Ensure builds are green on [Omnibus CE stable branch] and [Omnibus EE stable branch]

- [ ] Sync stable branches: CE, EE, and Omnibus to `dev`, CE and Omnibus to `github`

- [ ] Sync master branches to `dev` and `github`, as the CHANGELOG will be automatically updated on master during tagging

- [ ] If needed, sync tags for dependencies (`gitlab-shell`, `gitlab-workhorse`, `gitlab-pages`, `gitaly`) to `dev` and `github` (when applicable. Builds should fail if this is needed.)

- [ ] Check for any problematic migrations in EE (EE migrations include CE ones), and paste the diff in a snippet: `git diff <%= version.previous_tag(ee: true) %>..<%= version.stable_branch(ee: true) %> -- db/migrate db/post_migrate` =>

- [ ] Make sure [`omnibus-gitlab/<%= version.stable_branch %>` CHANGELOG.md][omnibus-stable-changelog] has an entry for each introduced change on [Omnibus CE stable branch]

- [ ] Make sure [`omnibus-gitlab/<%= version.stable_branch(ee: true) %>` CHANGELOG.md][omnibus-stable-ee-changelog] has an entry for each introduced change on [Omnibus EE stable branch]

- [ ] In `#releases`: I'm going to tag `<%= version.to_patch %>`

- [ ] Tag the `<%= version.to_patch %>` version using the [`release` task]:

      ```sh
      # In the release-tools project:
      bundle exec rake "release[<%= version.to_patch %>]"
      ```

- [ ] While waiting for packages to build, now is a good time to [create the blog post MR]. Look at previous MRs for examples. => BLOG_POST_MR

- [ ] Check progress of [EE packages build] and [CE packages build]. Ask in `#releases` to get confirmation on when the packages are indexed in packagecloud and ready for use in deployment.

- [ ] Get confirmation from a production team member to deploy staging. Use `!oncall prod` if needed to find who's on call.

- [ ] On video call, [deploy] release [`<%= version %>`](https://packages.gitlab.com/gitlab/gitlab-ee/packages/ubuntu/xenial/gitlab-ee_<%= version %>-ee.0_amd64.deb) to [staging.gitlab.com]

      ```sh
      # In the takeoff project:
      bin/takeoff-deploy -e staging -v <%= version.to_patch %>-ee.0
      ```

- [ ] Share the output of the migrations from the takeoff script in this issue via a snippet. Flag any migrations that take more than 5 minutes to run.

- [ ] Make sure that the database migrations complete in a timely manner. If you see a migration taking too long in staging, stop right there and ping `@db-team` in Slack. What you're witnessing has the potential to take down production for a long time. The statement "Don't worry: it'll be faster in production" is a false myth that has been disproven every single time that this has happened in the past.

- [ ] Create a "QA Task" issue in the [gitlab-org/release/tasks](https://gitlab.com/gitlab-org/release/tasks) repo.

- [ ] Wait for the QA Task deadline to pass.

- [ ] Get confirmation from a production team member to deploy canary. Use `!oncall prod` if needed to find who's on call.

- [ ] On video call, [deploy] release [`<%= version %>`](https://packages.gitlab.com/gitlab/gitlab-ee/packages/ubuntu/xenial/gitlab-ee_<%= version %>-ee.0_amd64.deb) to [canary.gitlab.com]

      ```sh
      # In the takeoff project:
      bin/takeoff-deploy -e canary -v <%= version.to_patch %>-ee.0
      ```

- [ ] Get confirmation from a production team member to deploy production. Use `!oncall prod` if needed to find who's on call.

- [ ] If downtime is expected, publicly [announce the deployment] on Twitter and with the GitLab.com deploy alert banner in the `#production` channel, 1 hour in advance

      ```
      !broadcast --start X:Y --end A:B "We are currently deploying GitLab EE <%= version.to_patch %>.
      For status updates, please follow https://twitter.com/GitLabStatus"
      ```

      ```
      !broadcast --start X:Y --end A:B "We are about to deploy GitLab EE <%= version.to_patch %> starting at X:Y UTC"
      ```

      ```
      !tweet "We are about to deploy GitLab EE <%= version.to_patch %> starting at X:Y UTC"
      ```

- [ ] On video call, [deploy] release [`<%= version %>`](https://packages.gitlab.com/gitlab/gitlab-ee/packages/ubuntu/xenial/gitlab-ee_<%= version %>-ee.0_amd64.deb) to GitLab.com

      ```sh
      # In the takeoff project:
      bin/takeoff-deploy -e production -v <%= version.to_patch %>-ee.0
      ```

- [ ] Tweet in the `#production` channel that the deployment has finished:

      ```
      !tweet "GitLab EE <%= version.to_patch %> has been deployed."
      ```

- [ ] From the [build pipeline], [manually publish public packages]

- [ ] Verify that packages appear on `packages.gitlab.com`: [EE](https://packages.gitlab.com/app/gitlab/gitlab-ee/search?q=<%= version.to_patch %>) / [CE](https://packages.gitlab.com/app/gitlab/gitlab-ce/search?q=<%= version.to_patch %>)

- [ ] Verify that Docker images appear on `hub.docker.com`: [EE](https://hub.docker.com/r/gitlab/gitlab-ee/tags) / [CE](https://hub.docker.com/r/gitlab/gitlab-ce/tags)

- [ ] Create the `<%= version %>` version on https://version.gitlab.com

- [ ] Deploy the blog post

- [ ] Tweet (prepare the Tweet text below or paste the tweet URL instead) in the `#releases` channel:

      ```
      !tweet "GitLab <%= version %> is released! BLOG_POST_URL DESCRIPTION OF THE CHANGES"
      ```

- [ ] Add [`omnibus-gitlab/<%= omnibus_version.tag %>` CHANGELOG.md][omnibus-tag-changelog] items to [`omnibus-gitlab/master` CHANGELOG.md][omnibus-master-changelog]

---

For references:

On gitlab.com

- https://gitlab.com/gitlab-org/gitlab-ce/commits/<%= version.stable_branch %>
- https://gitlab.com/gitlab-org/gitlab-ee/commits/<%= version.stable_branch(ee: true) %>
- https://gitlab.com/gitlab-org/omnibus-gitlab/commits/<%= version.stable_branch %>
- https://gitlab.com/gitlab-org/omnibus-gitlab/commits/<%= version.stable_branch(ee: true) %>

On gitlab.org

- https://dev.gitlab.org/gitlab/gitlabhq/commits/<%= version.stable_branch %>
- https://dev.gitlab.org/gitlab/gitlab-ee/commits/<%= version.stable_branch(ee: true) %>
- https://dev.gitlab.org/gitlab/omnibus-gitlab/commits/<%= version.stable_branch %>
- https://dev.gitlab.org/gitlab/omnibus-gitlab/commits/<%= version.stable_branch(ee: true) %>

[Merged MRs list]: https://gitlab.com/groups/gitlab-org/merge_requests?label_name%5B%5D=Pick+into+<%= version.to_minor %>&scope=all&sort=id_desc&state=merged
[Merging a CE stable branch into its EE counterpart]: https://gitlab.com/gitlab-org/release-tools/blob/master/doc/merge-ce-into-ee.md#merging-a-ce-stable-branch-into-its-ee-counterpart

[omnibus-stable-changelog]: https://gitlab.com/gitlab-org/omnibus-gitlab/blob/<%= version.stable_branch %>/CHANGELOG.md
[omnibus-stable-ee-changelog]: https://gitlab.com/gitlab-org/omnibus-gitlab/blob/<%= version.stable_branch(ee: true) %>/CHANGELOG.md

[Omnibus CE stable branch]: https://gitlab.com/gitlab-org/omnibus-gitlab/commits/<%= version.stable_branch %>
[Omnibus EE stable branch]: https://gitlab.com/gitlab-org/omnibus-gitlab/commits/<%= version.stable_branch(ee: true) %>
[CE packages build]: https://dev.gitlab.org/gitlab/omnibus-gitlab/commits/<%= version.stable_branch %>
[EE packages build]: https://dev.gitlab.org/gitlab/omnibus-gitlab/commits/<%= version.stable_branch(ee: true) %>

[omnibus-tag-changelog]: https://gitlab.com/gitlab-org/omnibus-gitlab/blob/<%= omnibus_version.tag %>/CHANGELOG.md
[omnibus-master-changelog]: https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/CHANGELOG.md

[`release` task]: https://gitlab.com/gitlab-org/release-tools/blob/master/doc%2Frake-tasks.md#releaseversion

[deploy]: https://gitlab.com/gitlab-org/takeoff#deploying-gitlab
[staging.gitlab.com]: https://staging.gitlab.com/
[canary.gitlab.com]: https://canary.gitlab.com/

[announce the deploy]: https://gitlab.com/gitlab-org/takeoff#announce-the-deployment

[manually publish public packages]: https://gitlab.com/gitlab-org/release-tools/blob/master/doc/publishing-packages.md
[build pipeline]: https://dev.gitlab.org/gitlab/omnibus-gitlab/pipelines?scope=tags

[create the blog post MR]: https://about.gitlab.com/handbook/marketing/blog/release-posts/#release-posts

/milestone %"<%= version.to_minor %>"
/due 7 days
