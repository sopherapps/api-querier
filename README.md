# api-querier [![Build Status](https://travis-ci.org/sopherapps/api-querier.svg?branch=master)](https://travis-ci.org/sopherapps/api-querier)

A simple wrapper around [axios](https://github.com/axios/axios) to work with remote API resources as though they were local

## Installation

This is an NPM package. It can be used with any of your favourite JavaScript/Typescript frameworks, anywhere axios runs

```bash
npm install --save api-querier
```

## Usage

This package provides two entities that you could use in your code.
It exports, by default, a function that can make an ApiQuerier object.

```typescript
        import makeApiQuerier, {IApiQuerier, IResourceQuerier} from 'api-querier';

        const resourceNames = ['books', 'tables', 'pens'];
        const baseURL = 'https://your_api_base_url.com';
        // Put the baseUrl of your api without trailing slash
        /*
        * In this example, the endpoints exposed by the remote API are
        *   - https://your_api_base_url.com/books
        *   - https://your_api_base_url.com/tables
        *   - https://your_api_base_url.com/pens
        */

        const apiQuerier: IApiQuerier = makeApiQuerier(resourceNames, baseURL,
        /*{...headers}, customResourceProcessorFunction // if you wish to have a different one
                                                        // but there is surely no need
         */);
        // apiQuerier is object whose keys are the resource names while
        // the values are IResourceQuerier objects

        const booksQuerier = apiQuerier['books'];
        const tablesQuerier = apiQuerier['tables'];
        const pensQuerier = apiQuerier['pens'];
        // signature of each individual querier /
        /*
        *interface IResourceQuerier {
            list: (queryObj?: any, headers?: any) => Promise<AxiosPromise<any>>;
            getOne: (id: string | number, headers?: any) => Promise<AxiosPromise<any>>;
            update: ((id: string | number, payload: any, headers?: any) =>
                     Promise<AxiosPromise<any>>);
            create: (payload: any, headers?: any) => Promise<AxiosPromise<any>>;
            destroy: (id: string | number, headers?: any) => Promise<AxiosPromise<any>>;
          }
        *
        */
       // each ResourceQuerier can list, getOne, update, destroy, create its items
       // each returning the same data an axios call would return; whose data is on
       // response.data

       const someAsyncFunction = () => {

         // list and filter books
         const booksQueryParams = {
           author: 'Some Name'
         };
         const someHeadersIfYouWant = {
           Authorization: 'Bearer some_token',
           'Content-Type': 'application/json',
         };
         const allBooksAuthoredBySomeName = await booksQuerier.list(booksQueryParams,
               someHeadersIfYouWant);

         // getOne table
         const tableOfIdRandomString = await tablesQuerier.getOne('RandomString',
               /*{...extraHeaders}*/);

         // update table
         await tablesQuerier.update('RandomString', {legs: 2}, /*, {...extraHeaders}*/);
         const updatedTableOfIdRandomString = await tablesQuerier.getOne('RandomString',
               /*{...extraHeaders}*/);
         updatedTableOfIdRandomString.data.legs == 2; // true

         // delete table
         const response = await tablesQuerier.delete(
              'RandomString', /*, {...extraHeaders}*/);
         const responseAfterDelete = await tablesQuerier.getOne('RandomString',
               /*{...extraHeaders}*/);
        responseAfterDelete.status === 404; //true

       }
```

## Advanced Docs

For more details of each class, interface, enum etc, visit [the TypeDoc generated docs site](https://sopherapps.github.io/api-querier).

## Acknowledgements

[Axios](https://github.com/axios/axios) is real nice!!

## License

Copyright (c) 2019 [Martin Ahindura](https://github.com/Tinitto) Licensed under the [MIT License](./LICENSE)
