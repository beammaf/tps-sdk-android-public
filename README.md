### TPS SDK - Android Guide

The TPS SDK is for interacting with the Beam Wallet platform to add, update or remove vehicles for integrated Ticketless Parking System. 

In order to use SDK you must be a registered developer with a provisioned API key.

## Requirements
* SDK Supports min api level 18.
* SDK Requires Java 8.
* BAPFES Authentication token. 

## Integration
* Java 8+ support should be added to project. To do that below code should be added to project level gradle file.
  ```groovy
  android {
      compileOptions{
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
  }
  ```
* SDK bundle is an .aar file. This aar file should be added as dependency to project level gradle file.
  ```groovy
  dependencies {
       compile(name:'tps-sdk', ext:'aar')
  }
  ```

* SDK Also requires some extra libraries (RX Java,Retrofit)

```groovy

        RX_VERSION = '2.1.4'
        RETROFIT_VERSION = '2.4.0'



 
        implementation "io.reactivex.rxjava2:rxjava:$RX_VERSION"
        implementation "io.reactivex.rxjava2:rxandroid:2.0.2"
    
        /* Networking */
        implementation "com.squareup.retrofit2:retrofit:$RETROFIT_VERSION"
        implementation "com.squareup.retrofit2:converter-gson:$RETROFIT_VERSION"
        implementation "com.google.code.gson:gson:2.8.2"
        implementation "com.squareup.retrofit2:adapter-rxjava2:$RETROFIT_VERSION"

 ```

# SDK Overview

## TPS TokenProvider

 * TPSTokenProvider is an interface in order to provide authentication token from BAPFES. This interface should be implemented to feed sdk with token.
 * Make sure to provide bapfes token before calling vehicle actions.
 ```java
 new TPSTokenProvider() {
                     @Override
                     public String getToken() {
                        return <<BAPFES TOKEN>>
                     }
                 }
 ```

## Initialize and Start Sdk
* In order to in initialize sdk, TPSSDK start() function should called from Instance

```java
void start(TPSServer server, TPSTokenProvider provider, BeamSdkInitializationListener listener);
```
* TPSServer: This is an enum in order to specify environment of tps.




Example Usage: 
```java

TPSSdk.getInstance().start(TPSServer.STAGING,this,new BeamSdkInitializationListener() {
      @Override public void onSdkInitialized() {
        sdkInitialized = true;
        view.hideProgress();
        // sdk initialized
        if (view.isAvailable()) {
          view.showInfo("Sdk initialized successfully");
        }
      }

      @Override public void onError(TPSError error) {
        if (view.isAvailable()) {
          view.hideProgress();
          view.showError(error.getMessage());
        }
      }
    });

```

## TPSSDK 
* All sdk functions can be accessed from TPSSDK object. It's a simple interface to access functionalities of TPS SDK

```java
  public interface TPSSdk {
  
    String LOG_TAG = "TPS-SDK";
  
    static TPSSdk getInstance() {
      return TPSSdkImpl.getInstance();
    }
  
    void start(TPSServer server, TPSTokenProvider provider, BeamSdkInitializationListener listener);
  
    void getRegisteredVehicles(TPSCallback<List<Vehicle>> listener);
  
    void addVehicle(Vehicle model, TPSCallback<Vehicle> listener);
  
    void updateVehicle(Vehicle model, TPSCallback<Vehicle> listener);
  
    void removeVehicle(Vehicle model, TPSCallback<Void> listener);

    void getTransactions(Vehicle vehicle,int page, Integer perPage, TPSCallback<List<Transaction>> listener);

    void getTransactions(int page, Integer perPage,TPSCallback<List<Transaction>> listener);
  
    void clear();
  }

```

#### TPSCallback\<T>
* An interface in order to get results for async operations.

#### TpsServer
* An enum object to specify target environment of sdk
```java
public enum TpsServer {
    DEV,
    PRODUCTION,
    STAGING
}

```


### Vehicle Operations
#### Vehicle
* This class is a model used by all vehicle operations.
```java
public class Vehicle implements Parcelable {

    
        private int id;
        private Plate plate;
        private String fundingSourceUUID;
        private String licencePlate;
        private String make;
        private String model;
        private String year;
        private Date validTo;
        private Date validFrom;
        private boolean isRental;
}
```

#### Get Registered Vehicles
```java
TPSSDK.getInstance().getRegisteredVehicles(new TPSCallback<List<Vehicle>>() {
                @Override
                public void onSuccess(List<Vehicle> result) {
                
                }

                @Override
                public void onError(TPSError error) {
                 
                }
            });
```
* This function returns registered vehicles which associated to logged in user.
#### Add Vehicle
```java
TPSSDK.getInstance().addVehicle(vehicle, new TPSCallback<Vehicle>() {
                    @Override
                    public void onSuccess(Vehicle result) {
                      
                    }

                    @Override
                    public void onError(TPSError error) {
                     
                    }
                });
```
* This function registers a vehicle. On callback returns vehicle with ID.
#### Update Vehicle
```java
TPSSDK.getInstance().updateVehicle(vehicle, new TPSCallback<Vehicle>() {
                @Override
                public void onSuccess(Vehicle result) {
                   
                }

                @Override
                public void onError(TPSError error) {
                
                }
            });
```
* The function takes a BWVehicle and updates the information related to TPSSDK.id 

#### Remove Vehicle
```java
TPSSDK.getInstance().removeVehicle(vehicle, new TPSCallback<Void>() {
                @Override
                public void onSuccess(Void result) {
           
                }

                @Override
                public void onError(TPSError error) {
               
                }
            });
```
* The function takes a vehicle model and remove the vehicle.

#### Parking Transaction History By Vehicle
```java
TPSSDK.getInstance().getTransactions(vehicle, page, perPage, new TPSCallback<Void>() {
                @Override
                public void onSuccess(Void result) {
           
                }

                @Override
                public void onError(TPSError error) {
               
                }
            });
```
* The function takes a vehicle model, page number and limit, it will return List of Transaction.

#### Parking Transaction History
```java
TPSSDK.getInstance().getTransactions(page, perPage, new TPSCallback<Void>() {
                @Override
                public void onSuccess(Void result) {
           
                }

                @Override
                public void onError(TPSError error) {
               
                }
            });
```
* The function takes a page number and limit, it will return List of Transaction.

## Version
* 1.2

