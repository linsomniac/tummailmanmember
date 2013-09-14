tummailmanmember README
=======================

Written by Sean Reifschneider <jafo@jafo.ca>  
License: GPLv2

Introduction
------------

This is a Postfix external policy service which will check at SMTP time to
see if the sender is allowed to post to the mailing list they are trying to
reach, and reject it at SMTP time (rather than generating a bounce).

Note that for it to reject you have to set the policy in the mailing list
for unknown memebers to be "reject".  Obviously, if you have it set to
"hold" or "discard" or "accept", this policy filter will be bypassed.  It
also honors the list of addresses to allow and reject.

A common problem is when a user is unable to post to the list because
(for example) they are sending from an address other than what they are
subscribed from.  The list admin can either add the senders address to the
list of addresses to accept, or the user can subscribe the other address
and set "nomail" on that subscription.

Installation
------------

This README expects that you have tummailmanmember installed in
/usr/local/sbin as seen below.  If you install it into another location,
you will need to change the path in the master.cf (referenced below) as
well.  Install tummailmanmember with:

    cp tummailmanmember /usr/local/sbin
    chmod 755 /usr/local/sbin/tummailmanmember

You need to list tummailmanmember in the `smtpd_recipient_restrictions`
list in the main.cf file, for example:

    smtpd_recipient_restrictions =
         permit_mynetworks
         check_policy_service unix:private/tummailmanmember
         reject_unauth_destination

Then you also have to list tummailmanmember in `master.cf`:

    tummailmanmember  unix  -       n       n       -       -       spawn
         user=list argv=/usr/local/sbin/tummailmanmember

This does rely on having access to the list of mailing list addresses in
the file "/var/lib/mailman/data/virtual-mailman.db" (the location of this
can be changed at the top of the tummailmanmember file).  This file is
created by mailman as lists are added and delete, if the MTA style in
mailman is set to 'Postfix'.