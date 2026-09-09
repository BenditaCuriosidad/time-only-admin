# Moderation panel

One static page, no build step, no server of its own: `index.html` talks to
Supabase with the same public anon key the app ships with. Everything a
moderator can do beyond what a member can do is decided by the database
(`public.is_moderator()`, checked inside every function it calls), so hosting
this file anywhere public gives nobody anything.

## What it shows

- **The books** — `ledger_health()`: system balance (must be 0), members,
  open reports, suspended members, drifted wallets.
- **Open reports** — with *Hide listing*, *Suspend* and *No action*; each one
  records who resolved it and how (`resolve_report`).
- **Members** — `moderator_members()`: name, area label, balance and held time
  as dots, active listings, open exchanges, reports against; *Suspend* /
  *Restore*.
- **Active listings** — with *Hide*.

There is deliberately no way to edit a wallet or a ledger entry. Corrections
are an `adjustment` pair that still sums to zero, done in SQL by a person.

## Who gets in

Anyone can sign in; only rows in `public.moderators` see anything. Add one:

```sql
insert into public.moderators (profile_id, note) values ('<profile uuid>', 'why');
```

Sign-in is email + password, or an emailed link. The link needs the panel's
URL in the project's auth redirect allow-list once it has a permanent address.

## Running it

Locally: `python3 -m http.server 8787 --bind 127.0.0.1` from this folder and
open `http://127.0.0.1:8787/`. Anywhere else: copy `index.html` to any static
host.
