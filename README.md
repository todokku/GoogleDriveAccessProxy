# **WIP**

## Google Drive Access Proxy
**Additional layer of access control for Google Drive API.**

### Why?
Google Drive API doesn't have single drive or directory level access scope. That means if someone has your token they can access ALL of your files.

### How?
GDAP is a Drive v3 API wrapper with additional checks:
Instead of Google API token service user must provide JWT token with payload like this:
```json
{
	"expires_at": "2020-06-30T12:30:00.000Z",
    "directories": [
        {
            "drive": "main",
            "path": "Full/Path/To/Folder",
            "access": [ "read", "write", "delete" ]
        },
        {
            "drive": "shared_media",
            "path": "Pictures/IRL",
            "access": [ "read" ]
        }
    ]
}
```
This token grants access to 2 directories: full access to `Full/Path/To/Folder` on drive `main`, and read-only access to `Pictures/IRL` on drive `shared_media`. Also, this token has an expiration date.

### Config
+ Available drive types: main_drive, shared_drive.
+ Drive `token` is your Google app token.
+ If your token leaked you can ban it in `banned_tokens`.
```json
{
	"secret": "your super secret key for validating JWT tokens",
	"drives": [
		{
			"name": "main",
			"type": "main_drive",
			"user": "shimmermare@gmail.com",
			"token": "eyJhbGciOiJIUzI1N.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6DIyfQ.SflKxwRJSMeKKF2dQssw5c"
		},
		{
			"name": "shared_media",
			"type": "shared_drive",
			"user": "admin@shimmermare.com",
			"token": "eyJhbGciOiJIUzI1N.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6DIyfQ.SflKxwRJSMeKKF2dQssw5c"
		}
	],
	"banned_tokens": [
		"eyJhbGciOiJIUzI1N.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6DIyfQ.SflKxwRJSMeKKF2dQssw5c"
	]
}
```

### Paths
+ `/access` - simple web access form: see directories accessible with token, file downloading, uploading and deletion.
+ `/api/*` - drive v3 API wrapper. See https://developers.google.com/drive/api/v3