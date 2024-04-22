# Repro for issue 7023

## Versions

firebase-tools: v13.7.3 and v13.6.0<br>
node: v20.10.0<br>
platform: macOS Sonoma 14.4.1

## Steps to reproduces

1. Run `firebase deploy --project PROJECT_ID`
   - Error is raised

```
=== Deploying to 'PROJECT_ID'...

i  deploying firestore
i  firestore: reading indexes from firestore.default.indexes.json...
i  firestore: reading indexes from firestore.test-db.indexes.json...
i  cloud.firestore: checking firestore.default.rules for compilation errors...
i  cloud.firestore: checking firestore.test-db.rules for compilation errors...
[2024-04-22T10:34:43.311Z] >>> [apiv2][query] POST https://firebaserules.googleapis.com/v1/projects/PROJECT_ID:test [none]
[2024-04-22T10:34:43.311Z] >>> [apiv2][body] POST https://firebaserules.googleapis.com/v1/projects/PROJECT_ID:test [omitted]
[2024-04-22T10:34:43.314Z] >>> [apiv2][query] POST https://firebaserules.googleapis.com/v1/projects/PROJECT_ID:test [none]
[2024-04-22T10:34:43.315Z] >>> [apiv2][body] POST https://firebaserules.googleapis.com/v1/projects/PROJECT_ID:test [omitted]
[2024-04-22T10:34:44.839Z] <<< [apiv2][status] POST https://firebaserules.googleapis.com/v1/projects/PROJECT_ID:test 200
[2024-04-22T10:34:44.840Z] <<< [apiv2][body] POST https://firebaserules.googleapis.com/v1/projects/PROJECT_ID:test {}
✔  cloud.firestore: rules file firestore.default.rules compiled successfully
[2024-04-22T10:34:44.859Z] <<< [apiv2][status] POST https://firebaserules.googleapis.com/v1/projects/PROJECT_ID:test 200
[2024-04-22T10:34:44.859Z] <<< [apiv2][body] POST https://firebaserules.googleapis.com/v1/projects/PROJECT_ID:test {}
✔  cloud.firestore: rules file firestore.test-db.rules compiled successfully
i  firestore: deploying indexes...
[2024-04-22T10:34:44.864Z] >>> [apiv2][query] GET https://firebaserules.googleapis.com/v1/projects/PROJECT_ID/releases pageSize=10&pageToken=
[2024-04-22T10:34:44.865Z] >>> [apiv2][query] GET https://firestore.googleapis.com/v1/projects/PROJECT_ID/databases/default/collectionGroups/-/indexes [none]
[2024-04-22T10:34:44.868Z] >>> [apiv2][query] GET https://firestore.googleapis.com/v1/projects/PROJECT_ID/databases/test-db/collectionGroups/-/indexes [none]
[2024-04-22T10:34:45.182Z] <<< [apiv2][status] GET https://firestore.googleapis.com/v1/projects/PROJECT_ID/databases/default/collectionGroups/-/indexes 404
[2024-04-22T10:34:45.182Z] <<< [apiv2][body] GET https://firestore.googleapis.com/v1/projects/PROJECT_ID/databases/default/collectionGroups/-/indexes {"error":{"code":404,"message":"Project 'PROJECT_ID' or database 'default' does not exist.","status":"NOT_FOUND"}}

Error: HTTP Error: 404, Project 'PROJECT_ID' or database 'default' does not exist.
[2024-04-22T10:34:45.432Z] Error Context: {
  "body": {
    "error": {
      "code": 404,
      "message": "Project 'PROJECT_ID' or database 'default' does not exist.",
      "status": "NOT_FOUND"
    }
  },
  "response": {
    "statusCode": 404
  }
}
```

## Notes

It looks like it's failing becuase it's trying to make a request to:

```
[2024-04-22T10:34:44.865Z] >>> [apiv2][query] GET https://firestore.googleapis.com/v1/projects/PROJECT_ID/databases/default/collectionGroups/-/indexes [none]
```

Using the ff `firebase.json` file:

```
{
  "firestore": {
    "rules": "firestore.default.rules",
    "indexes": "firestore.default.indexes.json"
  }
}
```

The request is being send to

```
[2024-04-22T10:40:35.039Z] >>> [apiv2][query] GET https://firestore.googleapis.com/v1/projects/PROJECT_ID/databases/(default)/collectionGroups/-/indexes [none]
```
