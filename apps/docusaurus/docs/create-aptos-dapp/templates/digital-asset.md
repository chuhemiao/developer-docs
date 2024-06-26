---
title: "Create Aptos Dapp Digital Asset Template"
slug: "digital-asset"
hidden: false
---

# Create Aptos Dapp Digital Asset Template

Digital Assets are the NFT standard for Aptos. The Digital Asset template provides an end-to-end NFT minting dapp. With this dapp, you can learn how NFTs work on Aptos, how to write a smart contract and how to write a front end connected to the contract. In addition, it offers a beautiful pre-made UI for a NFT minting dapp users can quickly adjust and deploy into a live server.

Read more about the [Aptos Digital Asset Standard](https://aptos.dev/standards/digital-asset)

The Digital Asset template provides 3 pages:

1. Public Mint NFT Page - A page for the public to mint NFTs.
2. Create Collection Page - A page for creating new NFT collections. This page is not accessible on production.
3. My Collections Page - A page to view all the collections created under the current Move module (smart contract). This page is not accessible on production.

## Public Mint Page

First run the dapp locally with `npm run dev`. You would see the public mint page with some data placeholders for content and images. Some of the content can be configured in the `frontend/config.ts` file and some will be pulled from an Aptos full node once you configure your collection.

### Configure a collection address

For the public mint page to fetch the collection, you will need to configure the collection address. First, assign the `collection_id` variable in the `frontend/config.ts` file with the collection address. If you have [created a collection](#create-a-nft-collection-page) with the tool, you can find it on the [My Collections Page](#my-collections-page).

### Modify static content

Once the collection address has been configured, view the `frontend/config.ts` file to change any static content on the page. You can also modify the code itself as you wish.

**How to add static images?**
The public mint page uses static images in the UI. Initially, the images are imported from the `frontend/assets/placeholder` folder. To use custom images, you should add the image you want to use to the `frontend/assets` folder (under any new folder you want to create) and then import the image as seen below in the `frontend/config.ts` file and add it under the section you want to have it.

```jsx
import MyImage from "@/assets/<my-new-folder>/my-image.png";
```

For example, to update an image in the “Our Team" section - add the image under the `frontend/assets/<my-new-folder>` folder, import the image as `import MyImage from "@/assets/<my-new-folder>/my-image.png";` and change the`img`property in the `ourTeam` section with `MyImage`.

## Create a NFT Collection Page

When you first running `npm run dev`, you will see the Public Mint page. To create a collection, click the “Create Collection” button, which will navigate you to create a new NFT collection.

### Publish the Move module

To create a new collection, we will need to publish the Move module on-chain.

The smart contract is built in a way that only the admin or a specific defined account can create a new collections.

Create or find the account you want to create a collection with. If you haven't created an account - simply use a Wallet, for example [Petra](https://petra.app/), to quickly [create an account](https://petra.app/docs/use#create-a-new-account).

1. Run `npm run move:init` - a command to initialize an account to publish the Move contract. When you run that command it will:

   - Generate a new CLI `.aptos/config.yaml` file that holds a profile with the account private key, account address, and network configuration.

2. To set who can create a new collection, edit the `.env` file and update `VITE_COLLECTION_CREATOR_ADDRESS` to be the address of the account allowed to create collections.

3. Run `npm run move:publish` to publish the contract. When you run that command it will:

```bash npm2yarn
npm run move:publish
```

- Save the module address to VITE_MODULE_ADDRESS in the `.env` file.

### Upgrade the Move module

When you make changes to your Move module, there are 2 kinds of changes, compatible and incompatible. You can learn [upgrade policy in details on docs](https://aptos.dev/move/book/package-upgrades/#compatibility-rules). When you make compatible changes, you can run `npm run move:upgrade` to upgrade previous published module. When you make incompatible changes, you can run `npm run move:publish` to publish to a new address.

### Connect a wallet

Once you have [published the move module](#publish-the-move-module), you will need to connect your wallet to submit transactions. Make sure your Wallet is set to the same account you used in the [previous section](#publish-the-move-module) to publish your Move module.
Also ensure that your wallet is set to the same network you selected to work with (e.g. testnet).

Now, you can connect your wallet by clicking on the "Connect Wallet" button at the top right, and start with creating your NFT collection.

### Upload Collection Files Data

To create a NFT collection you will need image files and metadata files.

Image files can be of any of the type - `"png", "jpg", "jpeg", "gif”.` But, make sure all image files have the same file extension. If some images have `jpeg` extension and some `jpg` extension, the upload will fail.

Metadata files are `json` files that hold the Collection data and each of the NFT data.

`create-aptos-dapp` requires you to upload a folder with all the relevant files, a folder structure should be

```jsx
assets/ // root folder
 collection.jpeg // the collection image
 collection.json // the collection metadata
 images/ // NFT images folder
  1.jpeg
  2.jpeg
  3.jpeg
 metadatas // NFT metadatas folder
  1.json
  2.json
  3.json
```

:::note
Make sure all image files have the same file extension. If some images will have `jpeg` extension and some `jpg` extension, the upload will fail.
:::

<!-- TODO: change ERC-1155 once finish rewriting the NFT standards page  -->

We recommend the developers follow the [ERC-1155 off-chain data](https://eips.ethereum.org/EIPS/eip-1155) schema to format their JSON files. One thing to note, since the image field will be updated with the Arweave link after this step, feel free to leave it empty.

Example of `collection.json` file

```jsx
// collection.json
{
  "name": "Aptos NFTs", // Name of the collection.
  "description": "My NFT Collection on Aptos.", // Description of the collection
  "image": "to_fill_after_upload", // This is the URL to the image of the collection, we will assign Arweave link so feel free to leave it empty
  "external_url": "https://your_project_url.io" // URL to an external website where the user can also view the image.
}
```

Example of an NFT metadata file. The metadata file should have the same name as the relevant image but with a `.json` extension. i.e if the nft image name is `1.jpeg` then a relevant metadata file should be `1.json`

```jsx
// 1.json
{
  "description": "nft 1 in collection", // Description of the NFT
  "image": "to_fill_after_upload", // This is the URL to the image of the NFT, we will assign Arweave link so feel free to leave it empty
  "name": "NFT 1", // Name of the NFT.
  "external_url": "https://your_project_url.io/1", // URL to an external website where the user can also view the image.
  "attributes": [ // Object array, where an object should contain trait_type and value fields. value can be a string or a number.
    {
      "trait_type": "meme_level",
      "value": "1"
    }
  ]
}

```

Once you've built the folder and you are ready to upload the collection files data, you can choose the folder you want to upload through the file input UI.

When adding the folder, it submits the files to [Irys](https://irys.xyz/), a decentralized asset server, that will store your files.

During the upload process, you will need to sign two messages to approve file uploading to Irys. Additionally, you may need to fund an Irys node. Read more about the process [here](https://docs.irys.xyz/hands-on/tutorials/uploading-nfts).

### Fill out collection details

Next, you want to fill out some collection details:

- Public mint start date - which defines when the public mint starts
- Public mint end date - which defines when the public mint ends (can leave it empty if there is no end date)
- Limit mint per address - which defines the limit of how many NFTs an account can mint
- (Optional) Royalty Percentage for each NFT - which defines the royalties for NFT marketplaces to read and respect when users trade NFTs
- (Optional) Mint fee per NFT - which defines the mint fee cost per nft
- (Optional) Mint for myself - which defines how many NFTs you would want to mint for yourself once the collection is created

### Submit a create collection transaction

Once everything has filled out, you should be able to click the “Create Collection” button and submit a create collection transaction. The only next step would be to approve the transaction on the wallet.

## My Collections Page

This page displays all the collections that have been created under the current Move module. You can click on the contract address, which redirects you to the Aptos Explorer site where you can see your collection.

When you are ready to use a collection on the Public Mint Page, you need to copy the collection address and assign it to the `collection_id` on the `frontend/config.ts` file.

Some stats are available on this page, like the amount of minted NFTs and Max Supply of the NFTs.

## How can I take it from here?

Remember, one of the goals of this template is to educate and provide a real life example on how a NFT minting dapp can be on Aptos. We provide some basic concepts and features but there is much more you can do for your dapp.

Some ideas you can try are:

1. Allowlist mint stages
2. Custom flows after someone mints a NFT (or even token gated experiences)
3. Check out our [TS SDK](https://github.com/aptos-labs/aptos-ts-sdk) to see what other [API queries](https://github.com/aptos-labs/aptos-ts-sdk/blob/main/src/api/digitalAsset.ts) you can use to support more features and fetch more data.

## Ready for Mainnet

If you started your dapp on testnet, and you are happy with your NFT collection testing, you will want to get the collection on mainnet.

Creating a collection on mainnet is the same flow as creating on testnet, but we need to change some configuration.

1. Change the `VITE_APP_NETWORK` value to `mainnet` in the `.env` file
2. Run `npm run move:init` to initialize an account to work against Mainnet
   1. If you already have an account you would like to use to publish the contract under, you can pass its private key when the prompt asks for that.
   2. If you are generating a new account, you need to transfer this account some APT on Aptos Mainnet since the tool can’t fund the account when it is against Mainnet.
3. Check: open `.aptos/config.yaml` file and see that you have a profile under the `{PROJECT_NAME}-mainnet` name (where `PROJECT_NAME` is specified in your `.env` file). In addition, open the `.env` file and check the `VITE_MODULE_ADDRESS` value is the same as the new profile's account account address.
4. Edit the `scripts/move/publish.js` file, and change the `minter` address to [`0x5ca749c835f44a9a9ff3fb0bec1f8e4f25ee09b424f62058c561ca41ec6bb146`](https://explorer.aptoslabs.com/object/0x5ca749c835f44a9a9ff3fb0bec1f8e4f25ee09b424f62058c561ca41ec6bb146/modules/code/mint_stage?network=mainnet) so it would use the Mainnet token-minter address.
5. Create or get the account you want to create a collection with, open the `.env` file and assign the account address as the `VITE_COLLECTION_CREATOR_ADDRESS` value.
6. Finally, run `npm run move:publish` to publish your move module on Aptos mainnet.
7. The next step would be to create a collection using this account. Simply follow [the instructions here](#create-a-nft-collection-page)

## Deploy to a live server

`create-aptos-dapp` utilizes Vite as the development tool. To deploy a Vite static site to a live server, you can simply follow [Vite deployment guide](https://vitejs.dev/guide/static-deploy). In a nutshell, you would need to:

1. Run `npm run build` to build a static site
2. Run `npm run preview` to see how your dapp would look like on a live server
3. Next, all you need is to deploy your static site to a live server, there are some options for you to choose from and can follow [this guide](https://vitejs.dev/guide/static-deploy#github-pages) on how to use each

## Update the look and feel of the dapp

This template is styled with [Tailwind CSS](https://tailwindcss.com/) and [shadcn/ui](https://ui.shadcn.com/). These libraries provide the app with a neutral and clean look and feel while leaving it open to a lot of customization so that you can make the app truly yours.

Please refer to the following questions in the FAQ to learn about how to customize the UI of your dapp:

- [What is Tailwind CSS?](../faq.md#what-is-tailwind-css)
- [What is `shadcn/ui`?](../faq.md#what-is-shadcnui)
- [How to modify the theme?](../faq.md#how-to-modify-the-theme)
- [How to add components?](../faq.md#how-to-add-components)
- [How to add colors?](../faq.md#how-to-add-colors)
- [How to add dark mode?](../faq.md#how-to-add-dark-mode)
