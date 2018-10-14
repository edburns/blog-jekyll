---
layout: post
title:  "Fetchmail Hangs: Approach to Debugging"
date:   2018-10-14 10:00 -0500
comments: false
---

## Executive Summary

*Symptom*: Fetchmail hangs when fetching messages.  When it happens in
fetchmail, it happens in Thunderbird as well.

*Cause*: Certain messages seem to cause the `stbeehive.oracle.com` IMAP
server to hang when fetching headers with the IMAP
([RFC-1730](https://tools.ietf.org/html/draft-ietf-imap-imap4-06))
`FETCH 1 RFC822.HEADER` command.

*Resolution*: This is a cheezy workaround: delete the message that
causes the hang and it seems to unblock the rest of the fetching.  You
can do this in Thunderbird or with IMAP directly.

## Details

For the past fifteen years I have been trying to avoid
[going quietly into that good night](https://www.poets.org/poetsorg/poem/do-not-go-gentle-good-night)
of using another email program other than my beloved and highly
customized Emacs/VM.  This means occasionally things will just stop
working as the world around me changes and I try to stay the same (at
least as far as
[MUAs](https://searchnetworking.techtarget.com/definition/mail-user-agent)
go).  This happened this week: fetchmail would hang.  I observed that
Thunderbird would hang also.

### Diagnosis steps

1. Enable verbose when fetching.
```
fetchmail -v -v -f fetchmailrc-oracle
```
2. Observe the IMAP protocol interaction with the server.  For example:
```
fetchmail: IMAP> A0122 FETCH 1 RFC822.HEADER
fetchmail: IMAP< * 1 FETCH (RFC822.HEADER {734}
```
  In this the case of this particular bug, it would hang on just such a
  header fetch.
3. Experiment with direct IMAP interaction, using the IMAP protocol
   interation from the preceding step as a guide.  Log in to the IMAP
   server with the following commands:
```
openssl s_client -crlf -connect stbeehive.oracle.com:993
```
With this transcript:
```
A0001 CAPABILITY
+* CAPABILITY IMAP4REV1 IDLE AUTH=PLAIN
+A0001 OK CAPABILITY completed
A0002 LOGIN "email address" <PASSWORD HERE>
+A0002 OK LOGIN completed
A0003 SELECT "INBOX"
+* 90 EXISTS
+* 0 RECENT
+* OK [UIDVALIDITY 4094122] UID validity status
+* OK [UIDNEXT 326362] Predicted next UID
+* FLAGS (\Seen \Deleted \Answered \Forwarded \Redirected \Flagged \Hidden \Draft $MDNSent)
+* OK [PERMANENTFLAGS (\Seen \Deleted \Answered \Forwarded \Redirected \Flagged \Hidden \Draft $MDNSent)] Permanent flags
+A0003 OK [READ-WRITE] SELECT completed
A0004 EXPUNGE
```

This technique enabled me to see what was going on.
