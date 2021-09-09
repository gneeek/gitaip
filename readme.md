# git-aip

## An AIP is a repo

Archival Information Packages are arbitrarly complex sets of metadata and digital content.  A digital preservation workflow needs to be executed against a SIP, which requires a potentially large series of individual compute tasks to be run.  This takes time, and requires different tools to be used to perform different tasks.  

The current approach to keeping track of all this activity in Archivematica is to create microservices to perform specific tasks and store the output of these microservices in a central database.  A workflow engine is responsible for keeping track of the activites that have been performed.  When all the required preservation activities are completed, a microservice runs to create an aip.  This reads all the metadata out of the database and writes it into a METS file, which is added to the file system along with the content, and wrapped in a bag, which is then usually wrapped in a container (7z, tar, etc).

The makes the production of an AIP into one very large very long running transaction.  If something goes wrong, you basically have to throw out what you have and go back to the beginning, starting with the SIP.

Once the AIP is created, the process of making changes is complicated and error prone.  It is not possible to add files or remove files, it is difficult to understand the changes that were made.

What if we got rid of the database, and replaced it with a git server (using git lfs or partial clone).  The first step once a SIP is ingested is to put it inside a parent directory and initialize the parent directory as a git repo.  

Any microservice that acts on a package would start by doing a git checkout.  It can checkout only the digital objects and metadata that it needs.  The microservice creates new metadata and/or new digital objects, and checks those into its local copy of git and pushes them back to the git server.  Then it is done.

The git commits would automatically track the committer and author of each change.  digital objects can be replaced with new versions, and only the server needs to keep all the versions.

When all the changes to a package are completed, and it is time to make an aip - all that is needed is to clone the entire repo with all history, and put that in a containr (tar, 7z) and store that in preservation storage.  You can delete the git repo from the git server.  Later if you want to make changes to an aip, you can copy it out preservation storage back into the git server, and run whatever preservation activites you want.  New changes are added just like changes made while the 'oroginal' aip was made.  

THe 'final' aip and any 'updates' are simply git tags.  named versions.  Git is a content addressable file system, it takes care of removing duplicates (only one copy of a file with a given checksum is stored, even if it is referenced from many locations in a tree).  Git can compress content as well.  



