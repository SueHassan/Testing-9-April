**Be sure to follow the [Security Releases guide](https://gitlab.com/gitlab-org/release-tools/blob/master/doc/security.md).**

- Picked into respective `stable` branches from the `dev/security` branch. [Merged MRs list]:

  - [ ] REFERENCE_TO_MR_TO_PICK

- [ ] **Push `ce/<%= version.stable_branch %>` to `dev` only: `git push dev <%= version.stable_branch %>`**

- [ ] **Push `ee/<%= version.stable_branch(ee: true) %>` to `dev` only: `git push dev <%= version.stable_branch(ee: true) %>`**

- [ ] Merge `ce/<%= version.stable_branch %>` into `ee/<%= version.stable_branch(ee: true) %>` following [the security process]

- [ ] Make sure [`omnibus-gitlab/<%= version.stable_branch %>` CHANGELOG.md][omnibus-stable-changelog] has an entry for each introduced change on [Omnibus CE stable branch]

- [ ] Make sure [`omnibus-gitlab/<%= version.stable_branch(ee: true) %>` CHANGELOG.md][omnibus-stable-ee-changelog] has an entry for each introduced change on [Omnibus EE stable branch]

- [ ] **Push `omnibus-gitlab/<%= version.stable_branch %>` to `dev` only: `git push dev <%= version.stable_branch %>`**

- [ ] **Push `omnibus-gitlab/<%= version.stable_branch(ee: true) %>` to `dev` only: `git push dev <%= version.stable_branch(ee: true) %>`**

- [ ] While waiting for tests to be green, now is a good time to start on [the blog post], **in a private snippet** on https://dev.gitlab.org/: BLOG_POST_SNIPPET

  - [ ] Ensure the blog post discloses as much information about the vulnerability as is responsibly possible. We aim for clarity and transparency, and try to avoid secrecy and ambiguity.

  - [ ] If the vulnerability was responsibly disclosed to us by a security researcher, ensure they're [publicly acknowledged] and thank them again privately as well.

- [ ] Ensure [tests are green on CE]

- [ ] Ensure [tests are green on EE]

- [ ] Check for any problematic migrations in EE (EE migrations include CE ones), and paste the diff in a snippet: `git diff <%= version.previous_tag(ee: true) %>..<%= version.stable_branch(ee: true) %> -- db/migrate db/post_migrate` =>

- [ ] Tag the `<%= version.to_patch %>` version using the [`release` task]:

      ```sh
      SECURITY=true bundle exec rake "release[<%= version.to_patch %>]"
      ```

- [ ] Check that [EE packages are built], [CE packages are built] and appears on `packages.gitlab.com`: [EE](https://packages.gitlab.com/app/gitlab/gitlab-ee/search?q=<%= version.to_patch %>) / [CE](https://packages.gitlab.com/app/gitlab/gitlab-ce/search?q=<%= version.to_patch %>). Ask in `#releases` to get confirmation on when the packages are indexed in packagecloud and ready for use in deployment.

- [ ] Get confirmation from a production team member to deploy staging. Use `!oncall prod` if needed to find who's on call.

- [ ] On video call, deploy [`<%= version %>`](https://packages.gitlab.com/gitlab/gitlab-ee/packages/ubuntu/xenial/gitlab-ee_<%= version %>-ee.0_amd64.deb) to [staging.gitlab.com]

      ```sh
      # In the takeoff project:
      bin/takeoff-deploy -e staging -v <%= version.to_patch %>-ee.0
      ```

- [ ] Although normally there aren't any schema changes on a security release, make sure that the database migrations complete in a timely manner. If you see a migration taking too long in staging, stop right there and ping `@db-team` in Slack. What you're witnessing has the potential to take down production for a long time. The statement "Don't worry: it'll be faster in production" is a false myth that has been disproven every single time that this has happened in the past.

- [ ] Create a "QA Task" issue in the [gitlab-org/release/tasks](https://gitlab.com/gitlab-org/release/tasks) repo.

- [ ] Wait for the QA Task deadline to pass.

- [ ] Get confirmation from a production team member to deploy canary. Use `!oncall prod` if needed to find who's on call.

- [ ] On video call, deploy [`<%= version %>`](https://packages.gitlab.com/gitlab/gitlab-ee/packages/ubuntu/xenial/gitlab-ee_<%= version %>-ee.0_amd64.deb) to [canary.gitlab.com]

      ```sh
      # In the takeoff project:
      bin/takeoff-deploy -e canary -v <%= version.to_patch %>-ee.0
      ```

- [ ] Get confirmation from a production team member to deploy production. Use `!oncall prod` if needed to find who's on call.

- [ ] Publicly [announce the deployment] on Twitter and with the GitLab.com deploy alert banner in the `#production` channel

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

- [ ] On video call, deploy [`<%= version %>`](https://packages.gitlab.com/gitlab/gitlab-ee/packages/ubuntu/xenial/gitlab-ee_<%= version %>-ee.0_amd64.deb) to [GitLab.com]

      ```sh
      # In the takeoff project:
      bin/takeoff-deploy -e production -v <%= version.to_patch %>-ee.0
      ```

- [ ] Tweet in the `#production` channel that the deployment has finished:

      ```
      !tweet "GitLab EE <%= version.to_patch %> has been deployed."
      ```

- [ ] Manually [publish the packages], one tag at a time to prevent 504 errors on our packages server.

- [ ] Create the `<%= version %>` version on https://version.gitlab.com

- [ ] Mark any applicable previous releases as vulnerable on https://version.gitlab.com.

- [ ] Check any sensitive information from the confidential security issues, and redact them if needed

- [ ] Create the blog post merge request

- [ ] Deploy the blog post

- [ ] Push `ce/<%= version.stable_branch %>` to all remotes

- [ ] Push `ee/<%= version.stable_branch(ee: true) %>` to all remotes

- [ ] Push `omnibus/<%= version.stable_branch %>` and `omnibus/<%= version.stable_branch(ee: true) %>` to all remotes

- [ ] Push CE, EE and omnibus tags to all remotes

- [ ] Make the confidential security issues public

- [ ] Tweet (prepare the Tweet text below or paste the tweet URL instead) in the `#releases` channel:

      ```
      !tweet "GitLab <%= version %> is released! BLOG_POST_URL DESCRIPTION OF THE CHANGES"
      ```

- [ ] Coordinate with the Marketing team to send out a security newsletter

- [ ] Cherry-pick the merges from the `security` branch into `master` and push to all remotes.

- [ ] Add [`omnibus-gitlab/<%= omnibus_version.tag %>` CHANGELOG.md][omnibus-tag-changelog] items to [`omnibus-gitlab/master` CHANGELOG.md][omnibus-master-changelog]

---

For references:
- https://dev.gitlab.org/gitlab/gitlab-ee/commits/<%= version.stable_branch(ee: true) %>
- https://dev.gitlab.org/gitlab/gitlabhq/commits/<%= version.stable_branch %>
- https://dev.gitlab.org/gitlab/omnibus-gitlab/commits/<%= version.stable_branch(ee: true) %>
- https://dev.gitlab.org/gitlab/omnibus-gitlab/commits/<%= version.stable_branch %>

[Merged MRs list]: https://dev.gitlab.com/groups/gitlabhq/merge_requests?label_name%5B%5D=Pick+into+<%= version.to_minor %>&scope=all&sort=id_desc&state=merged

[omnibus-stable-changelog]: https://gitlab.com/gitlab-org/omnibus-gitlab/blob/<%= version.stable_branch %>/CHANGELOG.md
[omnibus-stable-ee-changelog]: https://gitlab.com/gitlab-org/omnibus-gitlab/blob/<%= version.stable_branch(ee: true) %>/CHANGELOG.md

[tests are green on CE]: https://dev.gitlab.org/gitlab/gitlabhq/commits/<%= version.stable_branch %>
[tests are green on EE]: https://dev.gitlab.org/gitlab/gitlab-ee/commits/<%= version.stable_branch(ee: true) %>
[EE packages are built]: https://dev.gitlab.org/gitlab/omnibus-gitlab/commits/8-10-stable-ee
[CE packages are built]: https://dev.gitlab.org/gitlab/omnibus-gitlab/commits/8-10-stable

[omnibus-tag-changelog]: https://gitlab.com/gitlab-org/omnibus-gitlab/blob/<%= omnibus_version.tag %>/CHANGELOG.md
[omnibus-master-changelog]: https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/CHANGELOG.md

[Omnibus CE stable branch]: https://dev.gitlab.org/gitlab/omnibus-gitlab/commits/<%= version.stable_branch %>
[Omnibus EE stable branch]: https://dev.gitlab.org/gitlab/omnibus-gitlab/commits/<%= version.stable_branch(ee: true) %>

[EE packages are built]: https://dev.gitlab.org/gitlab/omnibus-gitlab/commits/<%= version.stable_branch(ee: true) %>
[CE packages are built]: https://dev.gitlab.org/gitlab/omnibus-gitlab/commits/<%= version.stable_branch %>

[`gitlab/gitlab-ee`]: https://packages.gitlab.com/gitlab/gitlab-ee
[`gitlab/gitlab-ce`]: https://packages.gitlab.com/gitlab/gitlab-ce

[`release` task]: https://gitlab.com/gitlab-org/release-tools/blob/master/doc%2Frake-tasks.md#releaseversion
[`patch_issue` task]: https://gitlab.com/gitlab-org/release-tools/blob/master/doc%2Frake-tasks.md#patch_issueversion

[staging.gitlab.com]: https://gitlab.com/gitlab-org/takeoff#deploying-gitlab
[canary.gitlab.com]: https://canary.gitlab.com/
[GitLab.com]: https://gitlab.com/gitlab-org/takeoff#deploying-gitlab

[publicly acknowledged]: https://about.gitlab.com/vulnerability-acknowledgements/
[the blog post]: https://gitlab.com/gitlab-org/release-tools/blob/master/doc/security.md#about-the-blog-post
[the security process]: https://gitlab.com/gitlab-org/release-tools/blob/master/doc/security.md#merging-ce-stable-into-ee-stable

[publish the packages]: https://gitlab.com/gitlab-org/release-tools/blob/master/doc/publishing-packages.md
