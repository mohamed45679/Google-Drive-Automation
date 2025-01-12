import os
from pydrive.auth import GoogleAuth
from pydrive.drive import GoogleDrive

def create_authenticate_server():
    """
    Create and authenticate connection between local device and Google Drive
    """
    gauth = GoogleAuth()
    # Try to load saved client credentials
    gauth.LoadCredentialsFile("credentials.json")
    if gauth.credentials is None:
        # Authenticate if they're not there
        gauth.LocalWebserverAuth()
    elif gauth.access_token_expired:
        # Refresh them if expired
        gauth.Refresh()
    else:
        # Initialize the saved creds
        gauth.Authorize()
    # Save the current credentials to a file
    gauth.SaveCredentialsFile("credentials.json")
    drive = GoogleDrive(gauth)
    return drive

def create_folder(path, name):
    """
    Create a local folder
    """
    folder_path = os.path.join(path, name.replace('/','_'))
    if not os.path.exists(folder_path):
        os.mkdir(folder_path)
        print(f'{name} is created!')

def create_drive_folder(drive, folderName, parent_folderId=None):
    """
    Create a drive folder
    """
    if parent_folderId == None:
        file_metadata = {
                'title': folderName,
                'mimeType': 'application/vnd.google-apps.folder'
            }
    else:
        file_metadata = {
                    'title': folderName,
                    'parents': [{'id': parent_folderId}],
                    'mimeType': 'application/vnd.google-apps.folder'
                }
    gfile = drive.CreateFile(file_metadata)
    gfile.Upload()
    print(f'{folderName} is created!')
    return gfile['id']

def upload_whole_folder(drive, local_folderName, drive_folderId):
    """
    upload all files inside a local folder to a drive folder
    """
    # iterate through files of local folder
    for i, file in enumerate(os.listdir(local_folderName)):
        filePath = os.path.join(local_folderName, file)
        # if there are folders inside the main folder, iterate through them
        if os.path.isdir(filePath):
            gfileId = create_drive_folder(drive, file, drive_folderId)
            upload_whole_folder(drive, filePath, gfileId)
        else:
            file_metadata = {
                'title': file,
                'parents': [{'id': drive_folderId}]
            }
            gfile = drive.CreateFile(file_metadata)
            gfile.SetContentFile(filePath)
            gfile.Upload()
            print(f"{i+1} file uploaded: {file}")
    print(f'Finished Uploading "{local_folderName}"!')

def download_whole_folder(drive, drive_folderId, local_folderName):
    """
    Download all files & folders inside a drive folder to a local folder
    """
    mimetypes = {
        # Drive Document files as MS Documents
        'application/vnd.google-apps.document': 'application/vnd.openxmlformats-officedocument.wordprocessingml.document',
        # Drive Sheets files as MS Excel SpreadSheets
        'application/vnd.google-apps.spreadsheet': 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',
        # Drive presentation files as MS PowerPoint
        'application/vnd.google-apps.presentation': 'application/vnd.openxmlformats-officedocument.presentationml.presentation'
        # see https://developers.google.com/drive/v3/web/mime-types
        }
    extensions = {
        'application/vnd.google-apps.document': '.docx', # Document files extension
        'application/vnd.google-apps.spreadsheet': '.xlsx', # Sheets files extension
        'application/vnd.google-apps.presentation': '.pptx' # Presentation files extension
    }
    # create local folder if doesn't exist
    create_folder(os.getcwd(), local_folderName)
    # get all files & folders inside drive folder
    file_list = drive.ListFile({'q': f"'{drive_folderId}' in parents and trashed=false"}).GetList()
    # iterate through files of drive folder
    for i, file in enumerate(file_list):
        # print the mimetype of the file you're trying to download
        #print(f"title: {file['title']}, mimeType: {file['mimeType']}")
        # if there are folders inside the main folder, iterate through them
        if file['mimeType'].split('.')[-1] == 'folder':
            create_folder(local_folderName, file['title'])
            folder_path = os.path.join(local_folderName, file['title'].replace('/','_'))
            download_whole_folder(drive, file['id'], folder_path)
        else:
            download_mimetype = None
            # check if the file one of googledocs, googlesheets, or googleslides
            if file['mimeType'] in mimetypes:
                download_mimetype = mimetypes[file['mimeType']]
                fileName = file['title'] + extensions[file['mimeType']]
                filePath = os.path.join(local_folderName, fileName)
                file.GetContentFile(filePath, mimetype=download_mimetype)
                print(f"{i+1} file downloaded: {fileName}")
            else:
                fileName = file['title']
                filePath = os.path.join(local_folderName, fileName)
                file.GetContentFile(filePath)
                print(f"{i+1} file downloaded: {fileName}")
    print(f'Finished Downloading "{local_folderName}"!')
