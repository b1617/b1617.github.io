---
title: "Coder votre premier smart contract avec Solidity 🇫🇷"
date: 2022-02-24T19:30:03+00:00
weight: 1
# aliases: ["/first"]
tags: ["fr", "solidity"]
author: "b1617"
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "Preréquis : Node et NPM ⚠️"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
---

L'objectif de ce thread est vous présenter les étapes pour développer un smart contract Account sur la blockchain Ethereum pour stocker des ethers et de consulter le solde.

# Définition

Un smart contract est un programme qui contient des fonctions, des données, une adresse.
De plus, un smart contract est considère aussi comme un compte.
Cela signifie que le contrat a aussi un solde et peut envoyer et recevoir des ethers.
![smart contract](/smartcontract.png/)
# Développement

```
mkdir smartcontract
cd smartcontract
npm init -y
```

## Environnement de développement Ethereum

Nous allons utiliser [Hardhat](https://hardhat.org/) qui est un environnement de développement Ethereum pour compiler, tester et déployer un smart contract.

```
npx hardhat
```

Répondez ✅ pour toutes les questions.

## Smart Contract

Dans le dossier contracts, créer un fichier Account.sol et coder le smart contract ci-dessous pour recevoir et consulter le solde.

```
//SPDX-License-Identifier: Unlicense
pragma solidity ^0.8.0;

contract Account {
    uint256 private account;

    function getBalance() public view returns (uint256) {
        return account;
    }

    receive() external payable {
        account += msg.value;
    }

    fallback() external payable {}
}
```

Le smart contract est composé de 3 fonctions et d'une variable "account" qui permet de savoir la quantité d'ethers que nous possédons.
En ce qui concerne les fonctions, nous avons la fonction getBalance() qui ne fait rien de plus que de retourner le solde.

Puis la fonction receive() qui permet de recevoir des ethers, donc chaque fois on reçois des ethers, on ajoute la quantité dans notre variable account.
Finalement, la fonction fallback() qui est utilisée lorsqu'une fonction inexistante est appelée dans notre contrat.

Après avoir codé notre smart contract, il faut compiler notre code pour sortir un code lisible par la Ethereum Virtual Machine (EVM).

```
npx hardhat compile
```

Une étape importante après le développement d'un smart contract, il faut tester avant de déployer car on ne peut pas revenir en arrière car le smart contract est immutable.
Pour tester, créer un fichier Account.test.js dans le dossier test et coder le test ci-dessous.

```
const { expect } = require("chai");

describe("Account contract", function () {
  it("should receieve token and show balance", async function () {
    // On récupère le smart contract
    const accountContract = await ethers.getContractFactory("Account");
    // On déploie le smart contract
    const Account = await accountContract.deploy();
    // On récupère le compte d'un utilisateur
    const [sender] = await ethers.getSigners();
    // L'utilisateur nous envoie 50 ethers
    const transaction = await sender.sendTransaction({
      to: Account.address,
      value: 50,
    });
    await transaction.wait();
    // On récupère le solde de notre compte
    const balance = await Account.getBalance();
    // On vérifie qu'on a bien récu les 50 ethers pour valider le test
    expect(balance).to.equal(50);
  });
});

```

Dans un test, on décrit le scénario d'utilisation de notre smart contract.
Dans notre cas, on valide la possibilité de recevoir et de consulter le solde.
Pour tester, lancer la commande .

```
npx hardhat test
```
