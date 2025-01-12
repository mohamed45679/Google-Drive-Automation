import os
from pydrive.auth import GoogleAuth
from pydrive.drive import GoogleDrive
import pydrive_utils as u

message = """
Choose the task you want to perform?\n\
    1) Create Folder\n\
    2) Upload entire folder\n\
    3) Download entire folder\n\
Enter [1/2/3]: """

def get_task(message=message):
    """
    Get user input for required task
    HINT: Use a while loop to handle invalid inputs
    """
    while True:
        try:
            task = input(message).lower()

            if task in ['1', '2', '3']:
                break
        except KeyboardInterrupt:
            print('No input taken!')
        else:
            print('\nThat\'s an invalid input!')
    return int(task)

def main():   
    print('Welcome')
    task = get_task()
    drive = u.create_authenticate_server()
    if int(task) == 1:
        print('Create Folder')
        folderName = input('Enter folder name\n')
        parent = input('Do you want a parent folder?\tEnter [y/n]: ')
        if parent == 'y':
            parent_folderId = input('Enter parent folder ID\n')
            u.create_drive_folder(drive, folderName, parent_folderId)
        else:
            u.create_drive_folder(drive, folderName)
    elif int(task) == 2:
        print('Upload Files')
        local_folderName = input('Enter local folder name\n')
        drive_folderId = input('Enter Drive folder ID (Target)\n')
        u.upload_whole_folder(drive, local_folderName, drive_folderId)
    else:
        print('Download Files')
        drive_folderId = input('Enter Drive folder ID\n')
        local_folderName = input('Enter local folder name (Target)\n')
        u.download_whole_folder(drive, drive_folderId, local_folderName)

if __name__ == "__main__":
    restart = 'y'
    while restart == 'y':
        main()
        restart = input('Do you want to perform anther task?\nEnter [y/n]: ').lower()