# RFC: Reducing Toil will decrease unecessary access to Production environments 

* Comment Deadline: `2023-06-01`
* Team Crew and Name: Platform Crew All Teams
* Authors:
  * [BillChapmanUSDS](https://github.com/BillChapmanUSDS)
  * [Contributing Author 1 Github Handle](https://github.com/ContributingAuthor1Handle)
  * [Contributing Author 2 Github Handle](https://github.com/ContributingAuthor2Handle)

* [Original RFC Pull Request](https://github.com/department-of-veterans-affairs/va.gov-platform-arch/pull/-1)

## Background
   The primary focus of our 2023 SRE efforts is Toil reduction. One of the primary evidences of Toil is the amount of access we grant to Production and Staging resoruces.
Some reasons we have been given for requesting exceptional access have been and these are all examples of Toil. 

* Sideqik does not have all the necessary features to manage jobs from the UI
* VFS teams need to troubleshoot issues with features that only have the appropriate state in Production
* Teams do not have a lower environment that fully mirrors production
* Data that is encrypted via the Rails application can only easily be decryped through the application (console access) 
* Terroform builds for EKS are currently being done manually

Reduction in toil can be accomplished in many ways, I think we can focus on better process, better automation, and distribution of effort to more apprpropriate places. 

## Motivation
   Reduce the overhead that platform and VFS teams have in the care and feeding of VA.gov services and provide better customer service experience across organizational boundaries. 

## Design

* Ensure we have mechanisms in place to catch and idenfiy exceptional access. This means that all access to production and staging needs to be appropriately logged and reported. Especially Rails console access. 
  ** Add logging to Rails console, Use AWS and EKS logging where available. We suggest we also prompt user to provide a reason when Rails Console is accessed in production. 
*  Design a dashboard that demonstrates toil and design an SLO around the available metrics. 
* Design solutions to the current list of known TOIL scenarios (see examples) 
  ** Sidekiq is open source, contribute the missing features to the UI
  ** Add end points for teams that need reports they are currently running from the shell
  ** Do not go directly to console for tasks that are available elsewhere. 
  ** Finish Platform Automation around EKS so tasks are not run manually
* Define a Devops Role for access purposes, Devops owns and is responsible for reducing Toil moving forward. Users without this role should not be granted production access. Users that commit directly to the app regularly should not also have production access. 

## Risks

* Reduced productivity if we do not provide appropriate mitigation for current exceptionan access needs. 
* Increased tension between platform and vfs teams as we limit and reduce access. 


## Alternatives

List the alternative approaches

* Disallow all requests for exceptional access for purposes of Toil that do not come with a ticket listing a mitigation strategy and timeline to remove this access. 
* Continue on the current path but doulbe the size of the Pllatform team to support VFS efforts as they expand., 


