# Myntkaup API

API fyrir Myntkaup.is.

## API Instances

Main Production API Instance (handles crucial functionality like bitcoin deposits & withdrawals): https://api.myntkaup.is

Mobile Production API Instance (used for mobile app, only has view and Trade permissions): https://api.mobile.myntkaup.com

Mobile staging API instance (connected to the staging mobile app and webapp): https://api.dev.mobile.myntkaup.com

The instances are configured with the environment file, documentation here: [Environment file documentation](docs/generic/environment-file.md).

## Endpoints that require Authentication

**Mobile appið notar JWT authentication, sjá í kafla um Mobile app endapunkta að neðan.**

#### WebApp authentication er eftirfarandi:

Til þess að senda authenticated requests þá þarf að senda 'Authorization' header með fyrirspurninni.

Eftirfarandi parametrar eru nauðsynlegir:

```json
{
    "HMACdigest": "[HMAC digest á nonce+apikey +sha256 á api secret]",
    "nonce": "[Núverandi Unix tími í millisekúndum, þessi parameter þarf að vera nýr því hann er skoðaður sérstaklega og sannreyndur]",
    "apikey": "[Myntkaup API key sem þú hefur fengið úthlutað]"
}
```

**Authorization format:** "_HMACdigest_ _apikey_ _nonce_"

**Dæmi:**

**Auth strengur:** "5C0D68C68CEFD6FE0AE822CB4E2F3ABF6F62EC63B41042B7C3091DDF5D8629E9 mNb9EKW5bA9B4tKSo3dB4Kc93WzlDuEcOX5O 1567949489267"

Síðan er hann convertaður yfir í Base64:

Authorization:"NUMwRDY4QzY4Q0VGRDZGRTBBRTgyMkNCNEUyRjNBQkY2RjYyRUM2M0I0MTA0MkI3QzMwOTFEREY1RDg2MjlFOSBtTmI5RUtXNWJBOUI0dEtTbzNkQjRLYzkzV3psRHVFY09YNU8gMTU2Nzk0OTQ4OTI2Nw=="

Strengurinn er síðan sendur með Authorization HTTP headernum.

**Dæmi um fyrirspurn:**

```javascript
const request = require("request");

const options = {
    method: "POST",
    url: "https://api.myntkaup.is/v1/coinbase/btc/address",
    headers: {
        Authorization:
            "NUMwRDY4QzY4Q0VGRDZGRTBBRTgyMkNCNEUyRjNBQkY2RjYyRUM2M0I0MTA0MkI3QzMwOTFEREY1RDg2MjlFOSBtTmI5RUtXNWJBOUI0dEtTbzNkQjRLYzkzV3psRHVFY09YNU8gMTU2Nzk0OTQ4OTI2Nw==",
        "Content-Type": "application/x-www-form-urlencoded",
    },
};

request(options, function(err, res, body) {
    if (err) throw new Error(err);

    // Success making API call
    console.log(body);
});
```

#### Hvernig á að smíða HMAC signature

Dæmi hvernig á að smíða strenginn:

```javascript
function createSignature() {
    const nonce = new Date().getTime(); // timestamp
    const message = nonce + MYNTKAUP_API_KEY;
    const digest = crypto
        .createHmac("sha256", MYNTKAUP_API_SECRET)
        .update(message)
        .digest("hex")
        .toUpperCase();

    return {
        hash: digest,
        nonce: nonce,
    };
}
```

#### Sentry error logging

Skrá sig inn á Sentry.io til þess að sjá yfirlit yfir villur sem upp koma.

### Aur endapunktar

Endapunktar til þess að senda Aur rukkun á notendur.

-   [Make Aur purchase request](docs/aur/aur-request.md) : `POST /payments/aur`
-   [Cancel Aur purchase request](docs/aur/cancel-aur-request.md) : `DELETE /payments/aur`

### Coinbase endapunktar

Endapunktar til þess að vinna með Coinbase API.

-   [Coinbase - Get ticker](docs/coinbase/coinbase-ticker.md) : `GET /v1/coinbase/ticker/:tickerId`
-   [Coinbase - Get fees](docs/coinbase/coinbase-fees.md) : `GET /v1/coinbase/fees`
-   [Coinbase - Get Account Transfers](docs/coinbase/coinbase-transfers.md) : `GET /v1/coinbase/transfers` (WILL BE DEPRECATED, USE /btc/transfers)
-   [Coinbase - Get all Accounts](docs/coinbase/coinbase-get-all-accounts.md) : `GET /v1/coinbase/accounts`
-   [Coinbase - Get all Coinbase Accounts](docs/coinbase/coinbase-get-all-coinbase-accounts.md) : `GET /v1/coinbase/accounts/coinbase` - **NEW**
-   [Coinbase - Get Order](docs/coinbase/coinbase-get-order.md) : `GET /v1/coinbase/order/:orderId`
-   [Coinbase - Get fills](docs/coinbase/coinbase-fills.md) : `GET /v1/coinbase/fills`
-   [Coinbase - Get history](docs/coinbase/coinbase-history.md) : `GET /v1/coinbase/history`
-   [Coinbase - Get fiat transfers](docs/coinbase/coinbase-fiat-transfers.md) : `GET /v1/coinbase/fiattransfers`
-   [Coinbase - Get a single CB Transfer](docs/coinbase/coinbase-single-transfer.md) : `GET /v1/coinbase/transfer/:transferId`
-   [Coinbase - Get fee estimate](docs/coinbase/coinbase-get-fee-estimate.md) : `GET /v1/coinbase/feeestimate`

#### BTC endapunktar

-   [Coinbase - Create Bitcoin Deposit address](docs/coinbase/coinbase-create-btc-address.md) : `POST /v1/coinbase/btc/address`
-   [Coinbase - Market buy Bitcoin](docs/coinbase/coinbase-buy-btc.md) : `POST /v1/coinbase/btc/buy`
-   [Coinbase - Market sell Bitcoin](docs/coinbase/coinbase-sell-btc.md) : `POST /v1/coinbase/btc/sell`
-   [Coinbase - Get Bitcoin Account](docs/coinbase/coinbase-get-btc-account.md) : `GET /v1/coinbase/btc/account`
-   [Coinbase - Withdraw Bitcoin](docs/coinbase/coinbase-withdraw-btc.md) : `POST /v1/coinbase/btc/withdraw`
-   [Coinbase - Get Bitcoin Account Transfers](docs/coinbase/coinbase-btc-transfers.md) : `GET /v1/coinbase/btc/transfers`

#### ETH endapunktar

-   [Coinbase - Create Ethereum Deposit address](docs/coinbase/coinbase-create-eth-address.md) : `POST /v1/coinbase/eth/address`
-   [Coinbase - Market buy Ethereum](docs/coinbase/coinbase-buy-eth.md) : `POST /v1/coinbase/eth/buy`
-   [Coinbase - Market sell Ethereum](docs/coinbase/coinbase-sell-eth.md) : `POST /v1/coinbase/eth/sell`
-   [Coinbase - Get Ethereum Account](docs/coinbase/coinbase-get-eth-account.md) : `GET /v1/coinbase/eth/account`
-   [Coinbase - Get Ethereum Account Transfers](docs/coinbase/coinbase-eth-transfers.md) : `GET /v1/coinbase/eth/transfers`
-   [Coinbase - Withdraw Ethereum](docs/coinbase/coinbase-withdraw-eth.md) : `POST /v1/coinbase/eth/withdraw`

#### USDC endapunktar

-   [Coinbase - Create USDC Deposit address](docs/coinbase/coinbase-create-usdc-address.md) : `POST /v1/coinbase/usdc/address` - **NEW**
-   [Coinbase - Get USDC Account](docs/coinbase/coinbase-get-usdc-account.md) : `GET /v1/coinbase/usdc/account` - **NEW**
-   [Coinbase - Get USDC Account Transfers](docs/coinbase/coinbase-usdc-transfers.md) : `GET /v1/coinbase/usdc/transfers` - **NEW**
-   [Coinbase - Withdraw USDC](docs/coinbase/coinbase-withdraw-usdc.md) : `POST /v1/coinbase/usdc/withdraw` - **NEW**

### Nexo endapunktar

Endapunktar til þess að vinna með Nexo API.

-   [Nexo - Create User](docs/nexo/nexo-create-user.md) : `POST /v1/nexo/user`
-   [Nexo - Get User Balance](docs/nexo/nexo-get-balance.md) : `GET /v1/nexo/user/balance?userId=someuserid`
-   [Nexo - Get User Deposit addresses](docs/nexo/nexo-get-depositaddresses.md) : `GET /v1/nexo/user/depositaddress?userId=someuserid`
-   [Nexo - Get User Withdraw fees](docs/nexo/nexo-get-withdrawfees.md) : `GET /v1/nexo/user/withdrawfees?userId=someuserid`
-   [Nexo - Get User Withdrawals](docs/nexo/nexo-get-withdrawals.md) : `GET /v1/nexo/user/withdrawals?userId=someuserid`
-   [Nexo - Send Withdraw Request](docs/nexo/nexo-send-withdraw-request.md) : `POST /v1/nexo/user/withdraw`
-   [Nexo - Verify Withdraw Request](docs/nexo/nexo-verify-withdraw-request.md) : `POST /v1/nexo/user/withdraw/verify`

### Arion endapunktar

Endapunktar til þess að vinna með Arion API.

-   [Arion - Get deposits](docs/arion/arion-get-deposits.md) : `GET /v1/arion/deposits?dateFrom={yyyy-mm-dd}&dateTo={yyyy-mm-dd}`
-   [Arion - Get withdrawals](docs/arion/arion-get-withdrawals.md) : `GET /v1/arion/withdrawals?dateFrom={yyyy-mm-dd}&dateTo={yyyy-mm-dd}`
-   [Arion - Get all transfers](docs/arion/arion-get-transfers.md) : `GET /v1/arion/transfers?dateFrom={yyyy-mm-dd}&dateTo={yyyy-mm-dd}`
-   [Arion - Get bank accounts](docs/arion/arion-get-bank-accounts.md) : `GET /v1/arion/bankaccounts`
-   [Arion - Do bank payment](docs/arion/arion-do-bank-payment.md) : `POST /v1/arion/payment`
-   [Arion - Verify account](docs/arion/arion-verify-account.md) : `GET /v1/arion/verifyaccount?ssn={ssn}&accountnumber={accountnumber}`

### Mobile App endapunktar

Endapunktar til þess að sækja gögn og interacta við Myntkaup mobile appið. Mobile app endapunktar nota JWT token authentication model í staðinn fyrir authentication leiðina sem er líst hér að ofan.

Notandi í Mobile appinu skráir sig inn í gegnum Island.is í gegnum /innskraning endapunktinn og fær þá Session token sem dugar í 60min og líka Refresh token sem gildir í 90 daga sem hægt er að nota til þess að re-authenticatea notandann í 90 daga.

-   [App - Electronic ID Authentication](docs/app/app-authenticate.md) : `POST /v1/app/auth`
-   [App - Get user](docs/app/app-get-user.md) : `GET /v1/app/user`
-   [App - Get CompanyRelations](docs/app/app-get-companyrelations.md) : `GET /v1/app/companyrelations`
-   [App - Login via CompanyRelation](docs/app/app-login-via-companyrelation.md) : `POST /v1/app/companyrelations/:ssn`
-   [App - Get activities](docs/app/app-get-activities.md) : `GET /v1/app/activity`
-   [App - Get Forex data](docs/app/app-get-forexdata.md) : `GET /v1/app/forex`
-   [App - Get Forex rates](docs/app/app-get-forexrates.md) : `GET /v1/app/forex/rates` - **NEW**
-   [App - Get Ticker data](docs/app/app-get-tickerdata.md) : `GET /v1/app/ticker/:tickerId`
-   [App - Refresh JWT token](docs/app/app-jwt-refresh.md) : `POST /v1/app/jwt/refresh`
-   [App - Set user Pin](docs/app/app-setpin.md) : `POST /v1/app/pincode`
-   [App - Add Bank deposit notification](docs/app/app-add-depositnotification.md) : `POST /v1/app/depositnotification`
-   [App - Add a FiatWithdrawal request](docs/app/app-add-fiatwithdrawal.md) : `POST /v1/app/fiatwithdrawal`
-   [App - Update user Questionnaire](docs/app/app-set-questionnaire.md) : `POST /v1/app/questionnaire`
-   [App - Send email code](docs/app/app-sendcode-email.md) : `POST /v1/app/email/sendcode`
-   [App - Verify email](docs/app/app-verify-email.md) : `POST /v1/app/email/verify`
-   [App - Send Aur request](docs/app/app-create-aur-purchase-request.md) : `POST /v1/app/payments/aur`
-   [App - Cancel Aur request](docs/app/app-cancel-aur-purchase-request.md) : `DELETE /v1/app/payments/aur`
-   [App - Lookup FiatDeposit (Aur request data)](docs/app/app-get-fiatdeposit.md) : `GET /v1/app/fiatdeposit/:id`
-   [App - Verify PromoCode FiatDeposit (Giftcode endpoint)](docs/app/app-verify-promocode.md) : `POST /v1/app/promocode/verify`
-   [App - Login testuser](docs/app/app-login-testuser.md) : `POST /innskraning/testuser`
-   [App - Set user pushtoken](docs/app/app-setpushtoken.md) : `POST /v1/app/pushtoken`
-   [App - Set user defaultFiatCurrency](docs/app/app-setdefaultfiatcurrency.md) : `POST /v1/app/defaultfiatcurrency` - **NEW**
-   [APP - Trade FX](docs/app/app-trade-fx.md) : `POST /v1/app/trade/fx` - **NEW**
-   [App - Get AvailableCoins](docs/app/app-get-availablecoins.md) : `GET /v1/app/availablecoins`

#### BTC endapunktar

-   [App - Market buy BTC (EUR)](docs/app/app-buy-btc.md) : `POST /v1/app/btc/buy`
-   [App - Market sell BTC (EUR)](docs/app/app-sell-btc.md) : `POST /v1/app/btc/sell`
-   [App - Market buy BTC (USD)](docs/app/app-buy-btc-usd.md) : `POST /v1/app/btc-usd/buy` - **NEW**
-   [App - Market sell BTC (USD)](docs/app/app-sell-btc-usd.md) : `POST /v1/app/btc-usd/sell` - **NEW**
-   [App - Add a Bitcoin withdrawal request](docs/app/app-add-bitcoinwithdrawal.md) : `POST /v1/app/bitcoinwithdrawal`

#### ETH endapunktar

-   [App - Market buy ETH](docs/app/app-buy-eth.md) : `POST /v1/app/eth/buy`
-   [App - Market sell ETH](docs/app/app-sell-eth.md) : `POST /v1/app/eth/sell`
-   [App - Market buy ETH (USD)](docs/app/app-buy-eth-usd.md) : `POST /v1/app/eth-usd/buy` - **NEW**
-   [App - Market sell ETH (USD)](docs/app/app-sell-eth-usd.md) : `POST /v1/app/eth-usd/sell` - **NEW**
-   [App - Add a Ethereum withdrawal request](docs/app/app-add-ethereumwithdrawal.md) : `POST /v1/app/ethereumwithdrawal`

#### USDC endapunktar

-   [App - Add a USDC withdrawal request](docs/app/app-add-usdcwithdrawal.md) : `POST /v1/app/usdcwithdrawal` - **NEW**

### Generic endapunktar

Aðrir endapunktar sem eru í notkun.

-   [Generic - Electronic ID Authentication](docs/generic/authenticate.md) : `POST /auth`
-   [Generic - Add Mailerlite subscriber](docs/generic/mailerlite-add.md) : `POST /mailerlite/add`
-   [Generic - Add Mailchimp subscriber](docs/generic/mailchimp-add.md) : `POST /mailchimp/add` (deprecated)
-   [Generic - Innskrá/Nýskrá notanda](docs/generic/islandis-login-register.md) : `POST /innskraning`
-   [Generic - Get Dashboard data](docs/generic/get-dashboard-data.md) : `GET /data/dashboard`
-   [Generic - Get Trades data](docs/generic/get-trades-data.md) : `GET /data/trades`
-   [Generic - Get FX Trades data](docs/generic/get-fxtrades-data.md) : `GET /data/fxtrades` - **NEW**
-   [Generic - Get User balance](docs/generic/get-user-balance.md) : `GET /data/userbalance`
-   [Generic - Get Bitcoin withdrawals](docs/generic/get-bitcoin-withdrawals.md) : `GET /data/bitcoinwithdrawals`
-   [Generic - Get Withdrawal Requests](docs/generic/get-withdrawal-requests.md) : `GET /data/withdrawalrequests` - **NEW**
-   [Generic - Get Bitcoin withdrawals by user](docs/generic/get-bitcoin-withdrawals-by-user.md) : `GET /data/bitcoinwithdrawalsbyuser`
-   [Generic - Get Balance history](docs/generic/get-balance-history.md) : `GET /data/balancehistory`
-   [Generic - Get Balance history v2](docs/generic/get-balance-history-v2.md) : `GET /v2/data/balancehistory`
-   [Generic - Get Bitcoin deposits](docs/generic/get-bitcoin-deposits.md) : `GET /data/bitcoindeposits`
-   [Generic - Get Aur deposits](docs/generic/get-aur-deposits.md) : `GET /data/aurdeposits`
-   [Generic - Get Bitcoin deposits by user](docs/generic/get-bitcoin-deposits-by-user.md) : `GET /data/bitcoindepositsbyuser`
-   [Generic - Get users](docs/generic/get-users.md) : `GET /data/users`
-   [Generic - Get movements](docs/generic/get-movements.md) : `GET /data/movements`
-   [Generic - Send a push notification](docs/generic/send-pushnotification.md) : `POST /pushnotification`
-   [Generic - Get exchange rates](docs/generic/get-exchangerates.md) : `GET /data/exchangerates`
-   [Generic - Get accurate FX rates](docs/generic/get-accuratefxrates.md) : `GET /data/accuratefxrate`
-   [Generic - Get withdraw monitors](docs/generic/get-withdraw-monitors.md) : `GET /data/withdrawmonitor`
-   [Generic - Get daily candles](docs/generic/get-daily-candles.md) : `GET /data/dailycandles`
-   [Generic - Add Pepcheck document](docs/generic/add-pepcheck.md) : `POST /pepcheck`
-   [Generic - Update User Residence](docs/generic/update-user-residence.md) : `POST /residence`
-   [Generic - Send an SMS via Twilio](docs/generic/send-twilio-sms.md) : `POST /sms`
-   [Generic - Trade FX](docs/generic/trade-fx.md) : `POST /trade/fx` - **NEW**

### Python endapunktar

Endapunktar sem kalla á python.myntkaup.com. Fraud detection, AML risk og önnur virkni.

-   [Python - Assess Crypto Withdrawal fraud risk](docs/python/python-fraud-detection.md) : `POST /v1/python/fraud` - **NEW**
-   [Python - Estimate Crypto Withdrawal ML risk](docs/python/python-ml-risk.md) : `POST /v1/python/ml_risk` - **NEW**

### Running the application

1. First ssh into the server: `ssh ubuntu@api.myntkaup.is -i ~/.ec2/awskey.pem`
2. Clone application to server via git clone: `git clone https://github.com/pmm1/myntkaup-api`
3. If application is already there, just do a `git pull origin master`
4. Do a: `npm install`
5. Daemonize via pm2: `pm2 start pm2.json`
6. Disco shit.
