# Create an account

In this section, you will learn how to make a simple Hedera account. Hedera accounts are the entry  point by which you can interact with the Hedera APIs. Accounts hold a balance of hbars used to pay for API calls for the various transaction and query types.

## Pre-requisites:

{% page-ref page="../introduction.md" %}

{% page-ref page="environment-set-up.md" %}

{% hint style="warning" %}
Note: This example uses **Hedera Java SDK 1.1.5.** The latest Hedera Java SDK version can be found [here](https://github.com/hashgraph/hedera-sdk-java), however may not be compatible with the following sample. 
{% endhint %}

## Step 1. Generate keys for the new account

You will need to generate a pair of keys to assign to the new account. 

```java
// Generate a new key pair
Ed25519PrivateKey newAccountPrivateKey = Ed25519PrivateKey.generate();
Ed25519PublicKey newAccountPublicKey = newAccountPrivateKey.publicKey;
```

## Step 2. Create a new account

To create a new account we will submit an account create transaction to the Hedera test network. The account minimally requires you to assign a public key to the account at creation. This means that the corresponding private key will be required to sign transactions for the new account. We will additionally have a starting balance of 1,000 tinybars. This initial balance is funded from your account to the new account. You can optionally choose to create an account with a zero balance. 

```java
//Create new account and assign the public key
TransactionId newAccount = new AccountCreateTransaction()
     .setKey(newAccountPublicKey)
     .setInitialBalance(1000)
     .execute(client);
```

Additional properties can be set for accounts [here](https://docs.hedera.com/guides/docs/sdks/cryptocurrency/create-an-account). 

## Step 3. Get the new account ID

When you issue a transaction that creates a new entity \(account, topic, etc\) the new entity ID is stored in the receipt of the transaction. You must request the receipt of the transaction to obtain the new account ID. Requesting a receipt is free of charge today.

```java
AccountId newAccountId = newAccount.getReceipt(client).getAccountId();

System.out.println("The new account ID is: " +newAccountId);
```

## Step 4. Verify the new account balance

Next, you will submit a query to the Hedera test network to return the balance of the new account using the new account ID. The current account balance for the new account should be 1,000 tinybars. 

```java
//Check the new account's balance
Hbar accountBalance = new AccountBalanceQuery()
     .setAccountId(newAccountId)
     .execute(client);

System.out.println("The new account balance is: " +accountBalance);
```

⭐ Congratulations! You have successfully completed the following:

* Created new a Hedera account with an initial balance of 1,000 tinybar
* Obtained the new account ID by requesting the receipt of the transaction
* Verified the starting balance of the new account by submitting an query to the network

You are now ready to transfer some hbar to the new account 🤑!

## Code Check ✅ 

What your code should look like at this point:

```java
import com.hedera.hashgraph.sdk.Client;
import com.hedera.hashgraph.sdk.Hbar;
import com.hedera.hashgraph.sdk.HederaStatusException;
import com.hedera.hashgraph.sdk.TransactionId;
import com.hedera.hashgraph.sdk.account.AccountBalanceQuery;
import com.hedera.hashgraph.sdk.account.AccountCreateTransaction;
import com.hedera.hashgraph.sdk.account.AccountId;
import com.hedera.hashgraph.sdk.crypto.ed25519.Ed25519PublicKey;
import com.hedera.hashgraph.sdk.crypto.ed25519.Ed25519PrivateKey;
import io.github.cdimascio.dotenv.Dotenv;

public class HederaExamples {

    public static void main(String[] args) throws InterruptedException, HederaStatusException {

        //Grab your account ID and private key from the .env file
        AccountId myAccountId = AccountId.fromString(Dotenv.load().get("MY_ACCOUNT_ID"));
        Ed25519PrivateKey myPrivateKey = Ed25519PrivateKey.fromString(Dotenv.load().get("MY_PRIVATE_KEY"));

        //Create your Hedera testnet client
        Client client = Client.forTestnet();
        client.setOperator(myAccountId, myPrivateKey);


        // Generate a new pair of keys
        Ed25519PrivateKey newAccountPrivateKey = Ed25519PrivateKey.generate();
        Ed25519PublicKey newAccountPublicKey = newAccountPrivateKey.publicKey;


        // Create new account and assign the public key
        TransactionId newAccount = new AccountCreateTransaction()
                .setKey(newAccountPublicKey)
                .setInitialBalance(1000)
                .execute(client);

        // Get the new account ID
        AccountId newAccountId = newAccount.getReceipt(client).getAccountId();

        System.out.println("The new account ID is: " +newAccountId);

        // Check the new account's balance
        Hbar accountBalance = new AccountBalanceQuery()
                .setAccountId(newAccountId)
                .execute(client);

        System.out.println("The new account balance is: " +accountBalance);
    }
}
```

