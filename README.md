tummailmanmember README
=======================

Written by Sean Reifschneider <jafo@jafo.ca>  
License: GPLv2

Introduction
------------

This is a Postfix external policy service which will check at SMTP
time to see if the sender is allowed to post to the mailing list.
If the sender is not on the various "allow" lists, and not a subscriber,
Postfix will reject the message at SMTP-time.  No bounce is generated,
preventing joe-jobs and sender spoofing.

*Note:* This only occurs for lists that are set to "reject" or "discard"
for non-member posting.  Also, this only occurs for the "unsubscribe",
and "leave" addresses, and the list posting address (however, this can
be configured at the top of the program).

If you have users who regularly post from an address that is not
subscribed, you can either subscribe that address and set "nomail" on it,
or list them in the "List of non-member addresses whose postings should be
automatically accepted" field of the "Privacy Options" / "Sender
filters" configuration.  Either of these will allow tummailmanmember to
recognize them as valid posters.

Installation
------------

This README expects that you have tummailmanmember installed in
/usr/local/sbin as seen below.  If you install it into another location,
you will need to change the path in the master.cf (referenced below) as
well.

Install tummailmanmember with:

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
created by Mailman as lists are added and deleted, if the MTA style in
Mailman `mm_cfg.py` is set to 'Postfix':

    #-------------------------------------------------------------
    # Uncomment if you use Postfix virtual domains (but not
    # postfix-to-mailman.py), but be sure to see
    # /usr/share/doc/mailman/README.Debian first.
    MTA='Postfix'
