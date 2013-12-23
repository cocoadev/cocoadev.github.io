---
layout: page
title: ABAddressBook
---

    + (ABAddressBook *)sharedAddressBook;

 *Returns the unique shared instance of ABAddressBook
 *The addressbook returned will be for the effective user ID at the time of the call. setuid calls can be used to change the current user if you are trying to look through addressbooks.


    - (NSArray *)recordsMatchingSearchElement:(ABSearchElement *)search;

 *Returns an array of record matching the given search element
  *Raises if search is nil
  *Returns an empty array if no matches


    - (BOOL)save;


  *Saves changes made since the last save
  *Return YES if successful (or there was no change)


    - (BOOL)hasUnsavedChanges;


   *Returns YES if they are unsaved changes
   *The unsaved changes flag is automatically set when changes are made


    - (ABPerson *)me;


   *Returns the person that represents the user
   *Returns nil if "me" was never set


    - (void)setMe:(ABPerson *)moi;


   *Sets "Me" to moi.
   *Pass nil to clear "Me"


    - (ABRecord *)recordForUniqueId:(NSString *)uniqueId;


   *Returns a record (ABPerson or ABGroup) matching a given unique ID
   *Raises if uniqueId is nil
   *Returns nil if the record could not be found


    - (BOOL)addRecord:(ABRecord *)record;


   *Adds a record (ABPerson or ABGroup) to the AddressBook Database
   *Raises if record is nil
   *Returns YES if the addition was successful


    - (BOOL)removeRecord:(ABRecord *)record;


   *Removes a record (ABPerson or ABGroup) from the AddressBook Database
   *Raises if record is nil
   *Returns YES if the removal was successful


    - (NSArray *)people;


   *Returns an array of all the people in the AddressBook database
   *Returns an empty array in case the DB doesn't contain any body


    - (NSArray *)groups;


   *Returns an array of all the groups in the AddressBook database
   *Returns an empty array in case the DB doesn't contain any groups



Category:PointlessInformation
.
