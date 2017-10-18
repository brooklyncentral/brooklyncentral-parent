# brooklyncentral-parent

Parent project, serving as the ancestor POM for all brooklyncentral projects.
It sets up a profile that can be used to deploy artifacts to sonatype automatically.

## Setup downstream projects

1. You need to add this `pom` as parent within you project's `pom.xml`:
   ```xml
   <parent>
       <groupId>io.brooklyn</groupId>
       <artifactId>brooklyncentral-parent</artifactId>
       <version>1.0.0</version>
   </parent>
   ``` 
2. Copy the entire folder `.circleci` and paste it in the root of your project.
3. Go to https://circleci.com/ and add your project to be built automatically for each commit and pull requests.

## Perform releases

### SNAPSHOT releases

`master` branch should only contains `SNAPSHOT` version. Upon every commit pushed on `master`, Circle CI will build, sign
then deploy `SNAPSHOT` version to sonatype automatically.

### Official releases

To perform an official release, simply create a branch `release/<version>`, update the version number in the code then push
your changes. Circle CI will pick up and build the branch but then hold for a manual action to deploy artifacts to sonatype.

You will need to log on [Circle CI](https://circleci.com/) and manually unhold the corresponding release workflow on [this page](https://circleci.com/gh/brooklyncentral/workflows/).

Finally, you will need to log into [sonatype](https://oss.sonatype.org/#stagingRepositories) then close and release the artifact built and deployed by Circle CI.

### Manual releases

You can always perform manual releases by running the following command:

```bash
mvn source:jar javadoc:jar deploy -DdeployTo=sonatype --settings .circleci/settings.xml
```

Note that you need to setup the following environment variables:


| Environment variable | Value                                                                                             |
| -------------------- | ------------------------------------------------------------------------------------------------- |
| SONATYPE_USER        | Sonatype user                                                                                     |
| SONATYPE_PASSWORD    | Sonatype password                                                                                 |
| GPG_EXECUTABLE       | GPG executable, e.g. `gpg`                                                                        |
| GPG_SECRET_KEY       | GPG secret key to use. You can get it be running: `gpg -a --export-secret-keys <KEY-ID> | base64` |
| GPG_PASSPHRASE       | The passphrase of the <KEY-ID>                                                                    |
