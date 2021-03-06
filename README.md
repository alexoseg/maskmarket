Original App Design Project
===

# MaskMarket

## Table of Contents
1. [Overview](#Overview)
1. [Product Spec](#Product-Spec)
1. [Wireframes](#Wireframes)
2. [Schema](#Schema)

## Overview
### Description
This app provides a marketplace for individuals to buy and sell masks to one another. In an open marketplace type fashion, individuals will be able to list their mask, adding photo, sizing, and style descriptions for each mask. They will have the ability to list whether the masks can be shipped or whether they can be picked up from a secure location. For individual buyers, they will have the opputunity to peruse selections within a geographic area and beyond, and purchase the masks straight through the app. The purchase will go through only after the product has been received.

### App Evaluation
- **Mobile:** This app is geared towards mobile for a few reasons. One, the location services to locate local mask sellers will be much simpler through mobile. Two, since these are individuals selling it will be easier to take photos of masks and upload them directly through the app. Three, providing push notifications about purchase requests is something done best through mobile.
- **Story:** I am part of online communities at home and I often find many people trying to sell their homemade masks with people having to dm to set up purchases. Paired with the high demand for masks right now, I think this app would be invaluable for individuals who are looking to help during the Pandemic and connecting them to community members.  
- **Market:** My market has a pretty broad scope in terms of both buyers and sellers considering anybody can sell masks. However, I do want to focus on making the interface as simple as possible since I have seen older individuals are the ones that have been creating the most masks and I would like the UI to not hinder their ability to share their life saving resources. The user base is a bit niche in that if there is a vaccine for COVID-19 then the app will not be as useful. 
- **Habit:** The average buyer will simply consume from the app but sellers will be able to create. This app won't be very "habit" forming but it does provide a useful and easily accessible platform for buying and selling. 
- **Scope:** I definitely believe that this project is within the scope of the timeframe we have to build the app. The main pain points will come from account creation, mask listing creation and setting up a backend to facilitate the purchase and sell flow. However, the app in and of itself is pretty well defined and I believe I can come up with a plan that is both challenging but not overwhelming. 

## Product Spec

### 1. User Stories (Required and Optional)

**Required Must-have Stories**

* User can create a mask listing 
* User can view mask listings 
* User can view their mask listings
* User can view mask listing details
* User can create an account
* User can login to account
* User can "purchase" a mask from mask listing
* Seller can view purchase requests 
* Seller can complete purchase request

**Optional Nice-to-have Stories**
* Users are displayed mask listings by geo location
* Stripe API to process payments
* Set meeting location through Maps (if not shipping)
* Create Profile Page with seller rating

### 2. Screen Archetypes

* Login Screen
   * User can login 
* Register Screen
   * User can register
* Mask Listing Screen (Tab One) 
    * User can view a feed of Mask Listing 
    * User can press a cell to view Mask Details
* Mask Details Screen 
    * User can purchase mask off of the screen 
    * User can view their listing info on this screen
* User Mask Info (Tab Two) 
    * User can see a list of masks they bought and masks they are selling 
* Mask listing Creation Screen 
    * User will be able to upload images and descriptions for the masks

### 3. Navigation

**Tab Navigation** (Tab to Screen)

* Tab One
    * Tab One will be a listing of the masks for sale
* Tab Two 
    * Tab two will have a listing of your bought masks and masks your selling 
        * Have a radio type button to change what is being viewed 
* Flow Navigation 
    * Pressing a mask cell will lead to a Mask Detail Screen 
    * Creating a mask listing will lead to a Mask listing Creation Screen


**Flow Navigation** (Screen to Screen)
* Login Screen 
    * Takes you to home page
* Registration Screen 
    * Takes you to home page
* Mask Listing Screen
   * Pressing a mask cell will lead to a Mask Detail Screen 
   * Purchasing will take you to a purchase confirmation screen 
       * Go back to home screen after
* Mask Listing Creation Screen
   * Leads you to another screen to add the name, photo, and description of the mask

## Wireframes
[Figma Wireframe](https://www.figma.com/file/JcjFMTe55J2f2rAdjxncS6/Mask-Market?node-id=0%3A1)

## Schema 
The following objects will constitue the app flow 
* User Object
* Mask Listing Object

### Models

#### Model: User

| Property       | Type         | Description              |
| :------------- | :----------: | -----------:             |
| objectId       | String       | unique id for the user   |
| username       | String       | username for the user    |
| email          | String       | email for the user       |

#### Model: Listings

| Property       | Type         | Description                             |
| :------------- | :----------: | -----------:                            |
| objectId       | String       | unique id for the listing               |
| createdAt      | Date         | date that the listing was created       |
| description    | String       | description for the mask listing        |
| title          | String       | title of the mask listing               |
| city           | String       | city of where the mask listing is based |
| state          | String       | state of where the mask listing is based| 
| author         | PFUser       | user that created the listing           |
| price          | Integer      | price of the mask listing               |
| purchased      | Boolean      | flag whether or not it is purchased     |
| purchasedBy    | PFUser       | user that purchased the mask listing    | 
| image          | File         | image for the mask listing              | 

### Networking

#### Neccessary Network Requests
* Login Screen 
    * (Read/GET) Fetch the logged in user's User object 
* Create Account Screen 
    * (Create/POST) Create a new user object
* Home Screen
    * (Read/GET) Fetch all of the current mask listings not purchased
* Detail Screen 
    * (Read/GET) Fetch the listing corresponding to the selected listing 
* Confirm Purchase Screen 
    * (Update/PUT) Update purchase to true 
    * (Update/PUT) Update purchasedBy to be the signed in user
* Purchases Screen
    * (Read/GET) Fetch listings with purchasedBy set to the current author
* Selling Screen
    * (Read/GET) Fetch listings with author set to the current author
* Creation Screen
    * (Create/POST) Create a new listing object 
        * purchased set to false and purchasedBy to nil

#### Parse Network Snippets
* Login Screen
    ```iOS
    [PFUser logInWithUsernameInBackground:@"myname" password:@"mypass"
      block:^(PFUser *user, NSError *error) {
    if (user) {
      // Do stuff after successful login.
    } else {
      // The login failed. Check error to see why.
    }
    }];
    ```
* Create Account Screen
    ```iOS
    PFUser *const user = [PFUser user];
    user.username = @"my name";
    user.password = @"my pass";
    user.email = @"email@example.com";

    [user signUpInBackgroundWithBlock:^(BOOL succeeded, NSError *error) {
      if (!error) {   
          // Open to the home screen
      } else {   
          NSString *errorString = [error userInfo][@"error"];  
      }
    }];
    ```
    
* Home Screen 
    ```iOS
    PFQuery *const query = [PFQuery queryWithClassName:@"Listings"];
    [query whereKey:@"purchased" isEqualTo:NO]; 
    query.limit = 20; 
    
    [query findObjectsInBackgroundWithBlock:^(NSArray *objects, NSError *error) {
        if (!error) {
            NSLog(@"Successfully retrieved %d scores.", objects.count);
            // Do something with the found objects
        } else {
            // Log details of the failure
            NSLog(@"Error: %@ %@", error, [error userInfo]);
        }
    }];
    ```
    
* Confirm Purchase Screen 
    ```iOS
    [query getObjectInBackgroundWithId:@"xWMyZ4YEGZ" 
                    block:^(PFObject *listing, NSError *error) {
          listing[@"purchased"] = @YES;
          listing[@"purchasedBy"] = PFUser.currentUser; 
    }]; 
    ```
    
* Purchases Screen
    ```iOS
    PFQuery *const query = [PFQuery queryWithClassName:@"Listings"];
    [query whereKey:@"purchased" isEqualTo:YES];
    [query whereKey:@"purchasedBy" isEqualTo:PFUser.currentUser];
    query.limit = 20; 
    
    [query findObjectsInBackgroundWithBlock:^(NSArray *objects, NSError *error) {
        if (!error) {
            NSLog(@"Successfully retrieved %d scores.", objects.count);
            // Do something with the found objects
        } else {
            // Log details of the failure
            NSLog(@"Error: %@ %@", error, [error userInfo]);
        }
    }];
    ```
    
* Selling Screen
     ```iOS
     PFQuery *const query = [PFQuery queryWithClassName:@"Listings"];
     [query whereKey:@"author" isEqualTo:PFUser.currentUser];
     query.limit = 20; 
    
     [query findObjectsInBackgroundWithBlock:^(NSArray *objects, NSError *error) {
        if (!error) {
            NSLog(@"Successfully retrieved %d scores.", objects.count);
            // Do something with the found objects
        } else {
            // Log details of the failure
            NSLog(@"Error: %@ %@", error, [error userInfo]);
        }
      }];
     ```
    
* Creation Screen 
    ```iOS
    PFObject *listing = [PFObject objectWithClassName:@"Listings"];
    listing[@"description"] = self.tempDescription;
    listing[@"title"] = self.tempTitle;
    listing[@"city"] = self.tempCity;
    listing[@"state"] = self.tempState; 
    listing[@"author"] = PFUser.currentUser; 
    listing[@"price"] = self.tempPrice; 
    listing[@"purchased"] = @NO; 
    listing[@"purchasedBy"] = nil; 
    listing[@"image"] = self.tempImage; 
    
    [listing saveInBackgroundWithBlock:^(BOOL succeeded, NSError * _Nullable error) {
      if (succeeded) {
        // The object has been saved.
      } else {
        // There was a problem, check error.description
      }
    }];
    ```

### API 

#### Stripe API
