# bitwarden-wrapper

With `lastpass` it was possible to write a simple search function and
provide formatted output.  e.g.

    lpass ls --format '%aN %al [id: %ai] %au' | grep -i "$@" | sed 's/.*\[id: \(.*\)\]/\1/' | while read id
    do
      lpass show $id
      echo ========================================
    done

With a command like that I could do

    % lastpass google
    Email/Google Acct1 [id: 1234569854575123456]
    Username: login1
    Password: password1
    URL: https://accounts.google.com/
    Email: login1
    Passwd: password1
    ========================================
    Email/Google Acct2 [id: 1234568839003123456]
    Username: login2
    Password: password2 
    URL: https://accounts.google.com/
    Email: login2
    Passwd: password2
    ========================================

We all know displaying passwords on the screen is bad, but sometimes
it's useful (e.g. when needing to enter passwords into a FireTV app)

This script does something similar with the bitwarden-cli `bw` command.
I renamed that to `bw.real` and then this wrapper will ensure the
session key is valid, do a search, and then use `jq` to format it.

    % bw google
    Google Acct1 [id: 12345685-b288-4264-8711-123456037871]
      Username: login1
      Password: password2
           URL: https://accounts.google.com/
    ========================================
    Google Acct2 [id: 12345664-dfbc-48dc-a8b8-123456037871]
      Username: login2
      Password: password2
           URL: https://accounts.google.com/
    ========================================

If an account has multiple URLs associated with it (e.g one for the
website and one for the android app) then it will be listed twice,
which isn't optimal but I can live with it.

# totpgen

This is a simple TOTP generator (matching Authy, Google Auth, etc etc).
You pass it a seed value and it will loop around providing the current
(and next) TOTP codes, displaying how long until the value expires

    % totpgen 1234 5678 9ABCD EF01
    Current = 039796 - Next = 770405 - Change in 25 seconds  

# totp

This is a wrapper around the `bw` and `totpgen` scripts.  If you have a TOTP
code saved in the database then it will pass that to `totpgen` and display
the TOTP code.

    % totp amazon
    Current = 015145 - Next = 637123 - Change in 18 seconds

