# Dir-functions-in-c++

#include <iostream>
#include <filesystem>
#include <string>
#include <windows.h>
#include <AclAPI.h>
#include <vector> // For memory management using std::vector

namespace fs = std::filesystem;

// Function to get the owner of a file or directory (Windows only)
std::wstring getOwner(const fs::path& filePath) {
    DWORD dwRes;
    PSECURITY_DESCRIPTOR pSD = NULL;
    PSID pOwnerSid = NULL;
    BOOL bOwnerDefaulted = FALSE;

    // Get the security descriptor for the file or directory
    dwRes = GetFileSecurity(filePath.c_str(), OWNER_SECURITY_INFORMATION, NULL, 0, &dwRes);
    if (dwRes == 0) {
        return L"Error retrieving owner";
    }

    // Use std::vector for dynamic memory allocation
    std::vector<BYTE> buffer(dwRes);
    pSD = reinterpret_cast<PSECURITY_DESCRIPTOR>(buffer.data());

    // Get the actual security descriptor
    if (!GetFileSecurity(filePath.c_str(), OWNER_SECURITY_INFORMATION, pSD, dwRes, &dwRes)) {
        return L"Error retrieving owner";
    }

    // Get the owner SID
    if (!GetSecurityDescriptorOwner(pSD, &pOwnerSid, &bOwnerDefaulted)) {
        return L"Error retrieving owner SID";
    }

    // Convert SID to a readable string (user-friendly name)
    wchar_t name[256];
    DWORD size = sizeof(name) / sizeof(name[0]);
    wchar_t domain[256];
    DWORD domainSize = sizeof(domain) / sizeof(domain[0]);
    SID_NAME_USE sidType;

    if (!LookupAccountSidW(NULL, pOwnerSid, name, &size, domain, &domainSize, &sidType)) {
        return L"Error looking up account name";
    }

    return std::wstring(domain) + L"\\" + std::wstring(name);
}

// Function to list files and directories in a given directory (dir /a behavior)
void listDir(const fs::path& dirPath, bool showOwner = false) {
    try {
        if (fs::exists(dirPath) && fs::is_directory(dirPath)) {
            std::wcout << L"Listing files and directories in: " << dirPath << std::endl;
            for (const auto& entry : fs::directory_iterator(dirPath)) {
                std::wcout << entry.path();
                if (showOwner) {
                    std::wcout << L"  Owner: " << getOwner(entry.path());
                }
                std::wcout << std::endl;
            }
        }
        else {
            std::wcerr << L"The specified path is not a valid directory." << std::endl;
        }
    }
    catch (const fs::filesystem_error& e) {
        std::wcerr << L"Error: " << e.what() << std::endl;
    }
}

// Function to list files and directories recursively (dir /s behavior)
void listDirRecursively(const fs::path& dirPath, bool showOwner = false) {
    try {
        if (fs::exists(dirPath) && fs::is_directory(dirPath)) {
            std::wcout << L"Listing files and directories recursively in: " << dirPath << std::endl;
            for (const auto& entry : fs::recursive_directory_iterator(dirPath)) {
                std::wcout << entry.path();
                if (showOwner) {
                    std::wcout << L"  Owner: " << getOwner(entry.path());
                }
                std::wcout << std::endl;
            }
        }
        else {
            std::wcerr << L"The specified path is not a valid directory." << std::endl;
        }
    }
    catch (const fs::filesystem_error& e) {
        std::wcerr << L"Error: " << e.what() << std::endl;
    }
}

// Main function to execute the user-selected option
int main() {
    std::wstring path;
    std::wstring option;
    bool continueLoop = true;

    while (continueLoop) {

        std::wcout << L"Enter the directory path (or 'exit' to quit): ";
        std::getline(std::wcin, path);

        // Check if the user wants to exit
        if (path == L"exit") {
            std::wcout << L"Exiting program." << std::endl;
            break;
        }

        if (path.empty()) {
            std::wcerr << L"Error: Directory path cannot be empty." << std::endl;
            continue;
        }

        fs::path dirPath(path);
        if (!fs::exists(dirPath) || !fs::is_directory(dirPath)) {
            std::wcerr << L"Error: The provided path is not a valid directory." << std::endl;
            continue;
        }

        // Prompt the user for option selection (/a, /s, or /q)
        std::wcout << L"Enter option (/a for list files, /s for list files recursively, /q for list files with owner): ";
        std::getline(std::wcin, option);

        // Perform action based on the option selected
        if (option == L"/a") {
            listDir(dirPath);  // List files and directories (non-recursive)
        }
        else if (option == L"/s") {
            listDirRecursively(dirPath);  // List files and directories recursively
        }
        else if (option == L"/q") {
            listDir(dirPath, true);  // List files with owner information
        }
        else {
            std::wcerr << L"Invalid option. Please enter either '/a', '/s', or '/q'." << std::endl;
        }
    }
    return 0;
}
 
