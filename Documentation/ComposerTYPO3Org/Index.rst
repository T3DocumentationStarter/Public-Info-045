


=============================
Blueprint: composer.typo3.org
=============================


:Proposal: 	Make composer.typo3.org future proof and TER obsolete

Owner/Starter 	Alexander Schnitzler
Participants/Members 	Helmut Hummel, Thomas Löffler
Status 	To be worked on
Current Progress 	30%
Topic for Gerrit 	N/A


Goals / Motivation
==================

At the moment the extension infrastructure of TYPO3 completely relies on the TER (TYPO3 extension repository), some kind of global directory with a soap api to release new extension versions. To be able to load ter extensions via composer, there currently is a service that fetches all ter extensions as xml (https://typo3.org/fileadmin/ter/extensions.xml.gz) and converts it to a packages.json, readable by composer. However, this is just a temporary solution.

The idea is to create a new middleware service that again reads the extension xml, this time but transforms it into real git repositories with a valid composer.json, that are tagged and pushed to a global accessible git server like stash or gitlab, so the actual git server can be used as a composer repository.
Concept

More and more extensions are developed with git, so it's a very unusual legacy approach of pushing the codebase of an extension version via a soap api to a repository like the TER. But although most extensions are already gitified, they are stored all over different git repository hosting providers like github, bitbucket, git.typo3.org or private solutions. The question is, how to collect these repositories and make them available as a composer repository on composer.typo3.org.

Currently this is done by a script, already explained some lines ago. However, this script completely relies on the TER, so people still need to upload their extensions to the TER in order to be available on composer.typo3.org. Another problem is, that composer.typo3.org has to fake a composer.json for each uploaded extension, no matter if an extension already has one, to be accessible by composer. Additionally that service completely relies on the ext_emconf.php to create the composer.json and pseudo versions/tags for each extension.

What needs to be achieved is a solution where people don't necessarily need to upload their extension to the TER to be available on composer.typo3.org. composer.typo3.org should be a real git server with real git repositories and tags, just like packagist.org. Anyway, composer.typo3.org needs a compat layer like it currently has. So, in the end extension developers should have the following options to choose from:

- Directly upload/maintain an extension on composer.typo3.org
- Tell composer.typo3.org where to find a publicly available version of the extension, so the middleware can import it
- Upload extensions to the TER, as usual and let the middleware import/convert the ext to a git repository

This way the dependency on ext_emconf.php is removed and people are encouraged to maintain their extensions like a classic composer package with a valid composer.json and the TER soap upload can get an expiry date.

[UPDATE] I talked to Helmut Hummel and we found a better idea of improving the current situation even faster. Instead of adding a git server where we push automatically converted extensions to, we simply install satis and let it act as a proxy for the generated composer.json, built from the TER xml and extension repositories that are on github, bitbucket, git.typo3.org and so on. I also talked to Thomas Löffler and he will help adding a checkbox to the extension information on typo3.org, so people can choose to let composer.typo3.org fetch their extension code from github etc. instead of the uploaded TER codebase.
Implementation Details

The server team needs to setup a git server, preferably atlassian stash. The middleware has to be created, that imports/converts extensions. The TER frontend (ter.typo3.org) needs to grab it's data from the git server, instead of it's own data.
Q & A

Q: Why not simply use packagist.org for all typo3 extension?

A: Because packagist is meant to be a repository for vanilla php packages and we don't want to flood packagist.org with several thousand typo3 extensions, especially not with outdated ones. These packages would be limited for usage with TYPO3 and thus not beneficial for users of other frameworks/libraries/...
Risks / Problems / Questions

    What if an extension is already published on packagist.org?

Issues and reviews

    https://forge.typo3.org/issues/68438
    https://forge.typo3.org/issues/68439

External links for clarification of technologies

    https://github.com/composer/satis

Category:

    Blueprints
