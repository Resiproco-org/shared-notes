In the backend code, under:
src/modules/transaction/controllers/transaction.controller.ts

```ts
@ApiCreatedResponseImplementation(Transaction)
@ApiNotFoundImplementation()
@Auth()
@UseInterceptors(AnyFilesInterceptor())
@Post()
async create(
	@Body() dto: CreateTransactionDto,
	@UploadedFiles(ParseTransactionItemFileValidation)
	files: Express.Multer.File[],
	@GetUser() user: User,
) {
	return await this.transactionService.create(dto, files, user);
}
```

if you fallow the .create (into):
src/modules/transaction/services/transaction.service.ts:639

```ts
async create(
	dto: CreateTransactionDto,
	files: Express.Multer.File[],
	requestUser: User,
): Promise<Transaction> {
	// ... some code

	// RIGHT BEFORE THIS CODE:
	const transaction = await this.transactionRepository.save({
	...
	})
}
```
I  am assuming that this is the code that actually saves the transaction into the database, but I haven't checked fully if there is other code that runs before this that adds anything else to the DB.

If we could create something here that checks if the file actually exists on the S3 instance, and if the files doesn't exist to make it reject...

You know the back-end for this app better, so let me know if I am missing some context or am looking at the wrong thing.