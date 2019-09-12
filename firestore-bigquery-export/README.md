# firestore-bigquery-export

**VERSION**: 0.1.0

**DESCRIPTION**: Sends realtime, incremental updates from a specified Cloud Firestore collection to BigQuery.



**CONFIGURATION PARAMETERS:**

* Deployment location: *Where should the extension be deployed? You usually want a location close to your database. For help selecting a location, refer to the [location selection guide](https://firebase.google.com/docs/functions/locations#selecting_regions_for_firestore_and_storage).*

* Collection path: *What is the path of the the collection that you would like to export?*

* Dataset ID: *What ID would you like to use for your BigQuery dataset? This extension will create the dataset, if it doesn't already exist.*



**NON-CLOUD FUNCTION RESOURCES CREATED**:

* fsexportbigquery (firebaseextensions.v1beta.function)



**DETAILS**: Use this extension to export the documents in a Cloud Firestore collection to BigQuery. Exports are realtime and incremental, so the data in BigQuery is a mirror of your content in Firestore.

The extension creates and updates a [dataset](https://cloud.google.com/bigquery/docs/datasets-intro) containing the following two BigQuery resources:

+   A [table](https://cloud.google.com/bigquery/docs/tables-intro) of raw data that stores a full change history of the documents within your collection. This table includes a number of metadata fields so that BigQuery can display the current state of your data. The principle metadata fields are `timestamp`, `document_name`, and the `operation` for the document change.
+   A [view](https://cloud.google.com/bigquery/docs/views-intro) which represents the current state of the data within your collection. It also shows a log of the latest `operation` for each document (`CREATE`, `UPDATE`, or `IMPORT`).

Whenever a document is created, updated, deleted, or imported in the specified collection, this extension sends that update to BigQuery. You can then run queries on this mirrored dataset.

Note that this extension only listens for _document_ changes in the collection, but not changes in any _subcollection_. You can, though, install additional instances of this extension to specifically listen to a subcollection or other collections in your database.

### Additional setup

Before installing this extension, you'll need to:

+   [Set up Cloud Firestore in your Firebase project.](https://firebase.google.com/docs/firestore/quickstart)
+   [Link your Firebase project to BigQuery.](https://support.google.com/firebase/answer/6318765)

#### Import existing documents

This extension only sends the content of documents that have been changed -- it does not export your full dataset of existing documents into BigQuery. So, to backfill your BigQuery dataset with all the documents in your collection, you can run the import script provided by this extension.

**Important:** Run the script over the entire collection _after_ installing this extension, otherwise all writes to your database during the import might be lost.

Learn more about using this script to [backfill your existing collection](https://dev-partners.googlesource.com/samples/firebase/mods/+/master/firestore-bigquery-export/guides/IMPORT_EXISTING_DOCUMENTS.md).

### Billing

This extension uses other Firebase or Google Cloud Platform services which may have associated charges:

+   Cloud Firestore
+   BigQuery
+   Cloud Functions

When you use Firebase Extensions, you're only charged for the underlying resources that you use. A paid-tier billing plan is only required if the extension uses a service that requires a paid-tier plan, for example calling to a Google Cloud Platform API or making outbound network requests to non-Google services. All Firebase services offer a free tier of usage. [Learn more about Firebase billing.](https://firebase.google.com/pricing)



**APIS USED**:

* bigquery-json.googleapis.com (Reason: Mirrors data from your Cloud Firestore collection in BigQuery.)



**ACCESS REQUIRED**:



This extension will operate with the following project IAM roles:

* bigquery.dataEditor (Reason: Allows the extension to configure and export data into BigQuery.)