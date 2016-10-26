# GoogleCloudPrintApi (Beta)
A .NET wrapper for Google Cloud Print API, used for server application, currently supports only Google Cloud Print 1.0. This library is based on .NET standard 1.4, can be run on .NET Core, .NET Framework, Xamarin.iOS, Xamarin.Android & Universal Windows Platform.

### Features
* Allows printer registration to Google Cloud
* Allows printer manipulation on Google Cloud
* Allows job retrieval from Google Cloud
* Allows printer sharing to Google Accounts

### Supported Platforms
* .NET Core 1.0
* .NET framework 4.6.1 or above
* Xamarin.iOS
* Xamarin.Android
* Universal Windows Platform

### How To Use

#### First-time token generation
	var provider = new GoogleCloudPrintOAuth2Provider(clientId, clientSecret);
	var url = provider.BuildAuthorizationUrl();
	/* Your method to retrieve authorization code from the above url */
	var token = await provider.GenerateRefreshTokenAsync(authorizationCode);
	

#### Initialize Google Cloud Print Client
	var client = new GoogleCloudPrintClient(provider, token);
	
#### Register printer
	var request = new RegisterRequest
	{
		Name = name,
		Proxy = proxy,
		Capabilities = capabilities
	};
	var response = await client.RegisterPrinterAsync(request);
	
#### List printers
	var request = new ListRequest { Proxy = proxy };
	var response = await client.ListPrinterAsync(request);
	
#### Get printer information
	var request = new PrinterRequest { PrinterId = printerId };
	var response = await client.GetPrinterAsync(request);
	
#### Update printer
	var request = new UpdateRequest
	{
		PrinterId = printerId,
		Name = nameToUpdate
	};
	var response = await client.UpdatePrinterAsync(request);
	
#### Delete printer
	var request = new DeleteRequest { PrinterId = printerId };
	var response = await client.DeletePrinterAsync(request);
	
#### Download printed job
	// Retrieve printed job list
	var fetchRequest = new FetchRequest { PrinterId = printerId };
	var fetchResponse = await client.FetchJobAsync(fetchRequest);
	var printJobs = fetchResponse.Jobs;
	
	// Select a printed job
	var printJob = printJobs.ElementAt(i);
	
	// Download and process ticket for the printed job
	var ticket = await client.GetTicketAsync(printJob.TicketUrl, proxy);
	/* Your method to process the ticket */
	
	// Download and save the document (in pdf format) for the printed job
	using (var documentStream = await client.GetDocumentAsync(printJob.FileUrl, proxy))
	using (var fs = File.Create(/* Your path for the document */))
	{
		documentStream.CopyTo(fs);
	}
	
	// Notify Google the printed job has completed processing
	var controlRequest = new ControlRequest
	{
		JobId = printJob.Id,
		Status = Models.Job.LegacyJobStatus.DONE
	};
	var controlResponse = await client.UpdateJobStatusAsync(controlRequest);
	

#### Share printer to Google User
	var request = new ShareRequest
	{
		PrinterId = printerId,
		Scope = /* Google account you want to share to */,
		Role = Role.USER
	};
	var response = await client.SharePrinterAsync(request);

#### Unshare printer from Google User
	var request = new UnshareRequest
	{
		PrinterId = printerId,
		Scope = /* Google account you want to unshare from */
	};
	var response = await client.UnsharePrinterAsync(request);

### License
This library is under [MIT License](https://github.com/salmonthinlion/GoogleCloudPrintApi/blob/master/LICENSE)

### Reference
[Google Cloud Print API Reference](https://developers.google.com/cloud-print/docs/proxyinterfaces)
	
	