# Overview
SGIdev has a software distribution service available at <http://ports.sgi.sh>. Developers creating ported software can upload compiled versions to this repository to make them available for other users.

# Getting set up
## Installing AWS CLI
The SGIdev software distribution service is backed by an Amazon S3 bucket. To upload files to this S3 bucket, the recommended free solution is the AWS CLI, however any S3 compatible graphical file explorer or S3 API access will also work.

Amazon provides instructions for installing the AWS CLI at: <https://docs.aws.amazon.com/cli/latest/userguide/install-bundle.html>.

## Obtaining Credentials
The credentials used to access Amazon S3 are in the form of an "AWS Access Key" and "AWS Secret Key." These credentials are one per individual and should not be shared with anyone else. Just to keep overhead low, please only request credentials once you have ported software to upload.

To obtain your personal credentials, please tag `@Elf#6313` in the SGIdev Discord `#general` channel. Please include the project(s) that you will be uploading builds for when requesting credentials.

Once you have obtained credentials and have installed the AWS CLI, run `aws configure` to set them up on the machine you will upload from and provide the Access Key and Secret Key when prompted.

* **AWS Access Key ID:** as supplied via Discord
* **AWS Secret Access Key:** as supplied via Discord
* **Default region name:** `us-east-2`
* **Default output format:** `None`

If you utilize the AWS CLI for more than just SGIdev uploads, we recommend setting up [named profiles](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html).

# Project organization
Projects are organized by software category and port name, under `ports.sgi.sh/{category}/{name}`, e.g. `ports.sgi.sh/editors/emacs`. To keep consistency, we suggest using the `{category}` and `{name}` values from the NetBSD ports list, found at: <http://cdn.netbsd.org/pub/pkgsrc/current/pkgsrc/README.html>. This will allow users to more easily locate software that they are familiar with.

# Performing uploads
To upload new software, utilize the AWS CLI as follows:
``aws s3 cp {package} s3://sgi-dev-public/{category}/{name}/`` (note the trailing slash).

This will make your software appear at: `http://ports.sgi.sh/{category}/{name}/{package}`

For example, if one performed the following command:
``aws s3 cp emacs-26.1.tgz s3://sgi-dev-public/editors/emacs/``, the software would then be available at `http://ports.sgi.sh/editors/emacs/emacs-26.1-201901171502.tgz`.

**Note:** Directory listings are generated once an hour (on the :21 minute mark), and each `index.html` (like all other objects) may be cached for *up to a day*. Directory listings thus will only eventually, not immediately, reflect the most up to date software listings.

# Removing old builds
While space in S3 is both cheap and unlimited, package maintainers are encouraged to remove erroneous uploads and packages with major security issues.

To remove a file, utilize the AWS CLI as follows:
``aws s3 rm s3://sgi-dev-public/{category}/{name}/{package}``

For example:
``aws s3 rm s3://sgi-dev-public/editors/emacs/``, the software would then be available at `http://ports.sgi.sh/editors/emacs/emacs-26.1-201901171502.tgz``

## Versioning & Caching
Please note that the SGIdev software repository is heavily cached in a Global CDN (Amazon CloudFront). This means that if you upload a file (e.g. `emacs-26.1-201901171502.tgz`), realize there is an issue with the package, and then re-upload over the same file name, people may still receive the old (non-functional) file for up to a day before the cache expires.

For this reason we suggest both not overwriting old files, as well as versioning your uploaded files by appending `-{YYYYMMDDhhmm}` to the file name before the suffix, where:

* `YYYY` is the four digit year
* `MM` is the two digit month
* `DD` is the two digit day
* `hh` is the two digit hour (24-hour system)
* `mm` is the two digit minute

If collaborating with others in multiple time zones, usage of `UTC` is recommended for these time/date stamps. This is just a suggested format that allows for easy sorting to find the most recently uploaded package.

# Mirroring
Other users are highly encouraged to mirror the SGIdev software repository, however we also encourage you to do this in an AWS native way which will lower our costs and provide you better performance. This is achieved by using native AWS access to the S3 bucket and _not_ a general spidering tool like HTtrack.

If you are interested in mirroring our software repository, please:

* Establish an AWS account
* [Find your AWS account ID](https://docs.aws.amazon.com/IAM/latest/UserGuide/console_account-alias.html#FindingYourAWSId)
* Tag `@Elf#6313` in the SGIdev Discord `#general` channel for cross-account access to the `sgi-dev-public` bucket.

You can then utilize a tool like `aws s3 sync` to easily, incrementally mirror the contents to either another S3 bucket or a local file system.

We will put more detailed mirroring instructions up shortly.
