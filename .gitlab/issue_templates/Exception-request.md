# Read me first and remove after you read!

This issue should be used to request that your MR be merged into an imminent RC as an exception.

Please read the ["Asking for an exception" docs](https://gitlab.com/gitlab-org/gitlab-ce/blob/master/PROCESS.md#asking-for-an-exception).


Any item inside of () should be removed before the issue is closed.
Please remove this notice once you read it.

------

# Exception request

(Use issue title in the format of:)
(YYYY-MM-DD: RELEASE_VERSION exception request for MERGE_REQUEST_REFERENCE)
(RELEASE_VERSION - should include an RC as well, eg. 10.4.0.rc1, 10.4.0, 10.4.1.)
(MERGE_REQUEST_REFERENCE should include the project name, eg. gitlab-org/gitlab-ce!123, gitlab-org/gitlab-ee!321.
)

Merge request to be considered for picking: MERGE_REQUEST_REFERENCE

## Why it needs to be picked

(Explain why it is crucial that the MR is picked.)
(What is the benefit?)
(What is the urgency?)
(What are alternatives?)

## Potential negative impact of picking

(Explain what could go wrong with the release if the MR is picked.)
(Include a description of the worst case scenario in case something breaks.)
(Include an estimate of the potential number of users affected by any negative impact.)
(Explain the steps you took to minimize the risk.)

## Sign-off

- [ ] Release manager: @[RELEASE_MANAGER_USERNAME]
- [ ] Engineering lead: @[ENGINEERING_LEAD_USERNAME]
- [ ] Engineering director: @[ENGINEERING_DIRECTOR_USERNAME]

**Note**: if you are the _last_ person to sign off, or about to close this
issue, please check that MERGE_REQUEST_REFERENCE has the correct milestone and
label set so that the release managers will pick it.

/label ~"Exception request"
