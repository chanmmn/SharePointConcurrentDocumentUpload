SharePoint concurrent document upload to Document Library

This post shows the code to simulate the concurrent upload to SharePoint document library using a single account.
Pre-requisite:

1. You have installed SharePoint SDK to your development environment https://chanmingman.wordpress.com/2018/10/07/ssis-could-not-load-file-or-assembly-microsoft-sharepoint-client/ 
2. You have Office 365 SharePoint online. 
3. You know C#.

In my Documents document library, I have a folder named TopFolder.

static void Main(string[] args)
{
	string filename = "";
	
	Thread t = new Thread(Go);
	
	t.Start();
	
	Thread t1 = new Thread(Go1);
	
	t1.Start();
	
	Thread t2 = new Thread(Go2);
	
	t2.Start();
	
	Thread t3 = new Thread(Go3);
	
	t3.Start();
	
	Thread t4 = new Thread(Go4);
	
	t4.Start();
	
	t.Join();
	
	t1.Join();
	
	t2.Join();
	
	t3.Join();
	
	t4.Join();
	
	Console.ReadLine();
	
}

static void Go()
{

	UploadFile("sperror.txt");

}

static void Go1()
{

	UploadFile("sperror1.txt");

}
static void Go2()
{

	UploadFile("sperror2.txt");

}
static void Go3()
{

	UploadFile("sperror3.txt");

}
static void Go4()
{

	UploadFile("sperror4.txt");

}
public static void UploadFile(string filename)
{

	string siteUrl = "https://account.sharepoint.com/DevSite";

	ClientContext clientContext = new ClientContext(siteUrl);

	clientContext.Credentials = SignIn.GetPassword();

	Web rootWeb = clientContext.Web;

	string filePath = filename;

	FileCreationInformation newFile = new FileCreationInformation();

	newFile.Content = System.IO.File.ReadAllBytes(filePath);

	newFile.Url = System.IO.Path.GetFileName(filePath);

	SP.List oList = clientContext.Web.Lists.GetByTitle(@"Documents");

	var folders = oList.RootFolder.Folders;

	clientContext.Load(folders);

	clientContext.ExecuteQuery();

	var folder = folders.Where(r => r.Name == "TopFolder");

	var folder1 = folder.FirstOrDefault();

	Microsoft.SharePoint.Client.File uploadFile = folder1.Files.Add(newFile);

	clientContext.Load(uploadFile);

	clientContext.ExecuteQuery();

	SP.ListItem item = uploadFile.ListItemAllFields;

	string docTitle = string.Empty;

	item["Title"] = docTitle;

	item.Update();

	clientContext.ExecuteQuery();

	Console.WriteLine("Done {0}", filename);
}

For the SignIn method is as below.

public static SharePointOnlineCredentials GetPassword()
{

	string password = "password";

	SecureString securePassword = new SecureString();

	foreach(char c in password)
	{

		securePassword.AppendChar(c);

	}

	return (new SharePointOnlineCredentials("account@account.onmicrosoft.com", securePassword));
}
