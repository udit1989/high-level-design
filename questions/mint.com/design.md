# Mint.com or Walnut design
mint.com is a simple system which keeps track of users financial transactions and helps the user to keep budget.

## Use cases 
1. The user should be able to connect with the bank account.
2. The user should be able to read data from the mobile.
3. The data should be synced with devices and backend.
4. Should be able to set budget.
5. Able to see category of spends.
6. Take preference in reading the data from the financial institutions, as compared to local device reads.

## Scale of system

1. This is a high availability system.
2. The data is not a shared data system.
3. Since availability is taken more into picture, and looking at the working of the device, eventual consistency works in between the devices and in updation of budget or transaction reads.
4. There is a possibility of bust traffic on paydays or month ends.
5. The traffic is considered and uneven distributed.
6. Take 10 million users and assuming 10 budgets per user.
7. Out of 10 million users, consider 5 million active users.
8. take max 5 devices per user


## Back of envelop 
Total number of users : ```10 million ```

Max traffic load : ``` 5*10 million``` = ```50 million ```devices.

```1 user``` does on an avg ```10 transaction``` everyday = ```10 * 5 million``` = ```50 million/day```.

total number of transaction per month = ```50 million * 30``` = 1500 million = ```1.5 billion```

Now, 
Transaction per second = 50 million/ 860400 = ```60 QPS```

And these read/writes are needed to be done, or done on polling on banking side.

```1 transcation = 50 bytes of data.```

Amount of data per month = ```50 bytes * 1.5 billion``` = 75 billion bytes = ```75 GB ```of data


### Latency 
The latency of the required user data is expected to be eventual.

Say 10 million users/devices requests twice a day for the data 

20 million requests

QPS of reads = 24 QPS to 30 QPS 


Now this is a Write heavy system, as we have more number of transaction data getting poles from the financial institutions.

## Base design 

### Schemas and tables 

1. User should be able to make the profile 
2. Transaction should be visible to the user 
3. Budget set should be available to user 

#### Tables 

User Table 

    - uid          ( number )   (pk)
    - u_name       ( varchar2 )
    - u_email      ( varchar2 )
    - u_address    ( varchar2 )
    - u_profile_uri( varchar2 )

Budget Table

    - b_id          (number)    (pk)
    - uid           (number)    (fk)
    - b_name        (varchar)
    - b_upper_value (number)
    - b_current     (number)
    - b_month       (timestamp)

Transaction Table

    - t_id          (number) (pk)
    - uid           (number) (fk)
    - t_time        (timestamp)
    - t_amount      (number)


In case of many-to-many relationship one needs to create a mapping table 

Say if there was a use-case where multiple budget can be associates with multiple users 
then there would have been a table 

users_budget table 

    - ub_id         (number) (pk)
    - uid           (number) (fk)
    - b_id          (number) (fk)


### APIS

1. get_user_data API

    To get all the user related data 
    ```
    GET /v1/get_user_data
    request
    {
        api_token,
        uid
    }
    response
    {
        Status code
        {
            uid          
            u_name       
            u_email      
            u_address    
            u_profile_uri
        }
    }
    ```

2. get_transaction

    To get the transaction, all or single 

    ```GET /v1/get_transaction/t_id```

3. get_budget

    To get the budget, all or single

    ```GET /v1/get_budget/b_id```

4. set_budget

    To set a budget

    ```SET /v1/set_budget```

4. add_transaction

    To add a transacation manually

    ```SET /v1/set_transaction ```

5. delete_budget

    To delete a budget

    ```DELETE /v1/delete_budget_b_id```

etc...


## Scaling of system

There are few key places where the system can choke and needs to be scalled properly 

1. The polling from the 3rd party, since the case is load of transactions, there is a requirement to insert a processing queue, which can redirect and keep buffer to controller service.

2. Sharding of data according to the userID or userID+transactionID, since there is a high number of writes available one needs to scale the dB, not because of the amount of data but actually the IOPS chocking.
So, for that 
    - one can keep the shards in different disks
    - or one can make bulk buffer writes ( still not reccomenended as not a long term practice )

The read and write DBs are required to be separate, where there can be just 1:1 master-slave arch between both of those.

3. Load balancers are reccomended on both the ends, since we have multiple input API servers, which can be statefull and so is on the transaction polling 3rd party LBs


## Diagram 
![Untitled-2022-07-05-1237](https://user-images.githubusercontent.com/25270515/186882595-78eb3835-ae95-4620-9cd0-d3f243b2a4b2.png)
