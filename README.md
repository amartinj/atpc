# ATPC (Affirm Trivial Payroll Checker) 🤖

This tool is a collection of small tools that can help "debugging" Affirm payroll issues

## Installation

To run atpc, make sure you have [Node.js](https://nodejs.org/en) > 18 installed.

To avoid issues with other projects it is recommended to install [NVM](https://github.com/nvm-sh/nvm) but you can use other ways to handle your node versions.

After it is installed you must install version 18 and enable it in the current shell:

```bash
nvm install 18
nvm use 18
```

## Usage

### Payroll info

This tool reads the PDFs of your payroll, please download all of them (or the ones taht you want to check) and add them to the /data/payroll folder.
They should follow the original file name of YYYY_MM_DD_...... to be able to parse the correct month associated with the payroll.

After you have all the files in the proper place then run

```npm run dev payroll -- --pasword XXXX```

Password is your DNI as it is the one needed to open the files.
That command will parse your payroll files and store all relevant information in a file named payroll.json in the /data folder
This command needs to be run again every time you add new pdfs to update the json file.
All check commands use this json file with the information as it is much easier to handle.

### Forma info

All forma commands (including downloading information) need a token to be able to connect to Forma API to get the token the first time (or when you get a 401-403) please do:

1. Run `npm run dev forma login`.
2. At the prompt, enter your email address, then hit Enter.
3. You'll be sent an email with a magic link. Go to your inbox and copy the link to your clipboard.
4. At the prompt, paste your magic link, then hit Enter.
5. You'll be logged in 🥳

To remember your login, ATPC stores a `.formanator.json` file in your home directory with your access token.

Forma commands:
* login: Gets the authentication token to be able to run other commands
* benefits: List a table with all the benefits in your account and the current available balance
* categoreis: List all categories that can be used to report a expense assoaciated with one of the benefits
* download: Downloads all the movements in the last 36 months (it can be adjusted with ```-- --months XX```)

The check wallets command requires all data to be downloaded before it can run: `npm run dev forma download`

### Checks info

Currently 3 checks have been implemented:

#### Wallets

```npm run dev check wallets```

This will try to match the reported amount in your payroll with the information it Forma, expense cut date is not fixed, this tool uses 14th of each month as the cut date but Affirm will not even do the cutoff on 14 or at the same time.
This will output a report with the calculated wallet expenses per month and the total in the payroll with a diff between the two.
As the cut date is impossible to predict usually some expenses are not correctly categorized so it is interesting to check if the diff in one month payroll is fixed the next month.

#### Sodexo

```npm run dev check sodexo```

This is just a simple reporter that shows the amount of Sodexo food reported in the payroll, as this tool does not connect with Sodexo it is just to be able the see the amount easily.
If examples are provided I can include other Sodexo related data.

#### RSU

```npm run dev check rsu```
or
```npm run dev check rsu -- --api <api>```

This report does need a your Schawb data to run, you can download it from https://client.schwab.com/app/accounts/history/# select "Equity Award Center" and "Date Range" -> "All" click Search and then click on "Export" on the top of the page, select JSON and store the file in /data, it should be named as "EquityAwards....json" and a single file with that name format should exist in the folder.

This report crosses data from your payroll and schwab and tells you the number of RSUs vested per month how many were sold/kept (and price) based on schwab data and the amount (money)
reported in your payroll (as sold/kept and as taxes), it also does the calculation of the conversion rate for the payroll info as sold/kept rate should match.
You can also pass an API token for https://exchangerate.host/ to be able to pull exchange rates for the vesting days and compare it with the calculated ones.
There is a known issue with how the payroll were created and Affirm has always created the payroll wrong reporting as sold the amount of RSUs that were kept (that ususally is higher) that's why another extra couple of columns is added with the inverted exchange rate as otherwsie it will never match.

## Contributing

Changes to this project are verioned using [Semantic Versioning](https://semver.org/) and released to `npm` automatically using [`semantic-release`](https://github.com/semantic-release/semantic-release).

Commit messages must follow [Angular Commit Message Conventions](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#-commit-message-format) so `semantic-release` knows when to release new versions and what version number to use.
