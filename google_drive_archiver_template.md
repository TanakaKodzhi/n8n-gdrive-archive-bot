{
  "name": "Архиватор креативов из Google Drive",
  "nodes": [
    {
      "parameters": {
        "content": "### 📌 Инструкция по настройке\n1. **Telegram Trigger / Telegram Node**: выберите ваши учетные данные Telegram Bot API.\n2. **Search files and folders**: добавьте Google Drive OAuth2 учетные данные и замените `YOUR_SOURCE_FOLDER_ID` на ID исходной папки.\n3. **Download file**: выберите ваши Google Drive учетные данные.\n4. **Upload file**: выберите Google Drive учетные данные и замените `YOUR_TARGET_FOLDER_ID` на ID папки назначения.\n5. Запуск происходит, когда боту в Telegram отправляют слово `готово`.",
        "height": 190,
        "width": 460,
        "color": 6
      },
      "type": "n8n-nodes-base.stickyNote",
      "typeVersion": 1,
      "position": [
        380,
        -320
      ],
      "id": "428b10f9-cf16-419b-aef5-2b449e77d2b5",
      "name": "Sticky Note"
    },
    {
      "parameters": {
        "updates": [
          "message"
        ],
        "additionalFields": {}
      },
      "type": "n8n-nodes-base.telegramTrigger",
      "typeVersion": 1.5,
      "position": [
        0,
        0
      ],
      "id": "3f7c051e-c6b6-44c9-b2ff-e575180a838e",
      "name": "Telegram Trigger"
    },
    {
      "parameters": {
        "conditions": {
          "options": {
            "caseSensitive": true,
            "leftValue": "",
            "typeValidation": "strict",
            "version": 3
          },
          "conditions": [
            {
              "id": "00026646-dfe8-46a5-940f-832c0cdbfb41",
              "leftValue": "={{ $json.message.text.trim().toLowerCase() }}",
              "rightValue": "готово",
              "operator": {
                "type": "string",
                "operation": "equals"
              }
            }
          ],
          "combinator": "and"
        },
        "options": {}
      },
      "type": "n8n-nodes-base.if",
      "typeVersion": 2.3,
      "position": [
        224,
        0
      ],
      "id": "aa9a2c67-0b82-4710-b953-5d97468f5485",
      "name": "If"
    },
    {
      "parameters": {
        "operation": "compress",
        "binaryPropertyName": "={{ Object.keys($binary).join(',') }}",
        "fileName": "creatives_pack.zip"
      },
      "type": "n8n-nodes-base.compression",
      "typeVersion": 1.1,
      "position": [
        1040,
        -96
      ],
      "id": "78b4d78b-a5c1-4757-8654-a32f2bc8b2b9",
      "name": "Compression"
    },
    {
      "parameters": {
        "aggregate": "aggregateAllItemData",
        "options": {
          "includeBinaries": true
        }
      },
      "type": "n8n-nodes-base.aggregate",
      "typeVersion": 1,
      "position": [
        864,
        -96
      ],
      "id": "e227662c-e4d9-47d0-85e5-7ea06c4e78c6",
      "name": "Aggregate"
    },
    {
      "parameters": {
        "resource": "fileFolder",
        "returnAll": true,
        "filter": {
          "folderId": {
            "__rl": true,
            "value": "YOUR_SOURCE_FOLDER_ID",
            "mode": "list",
            "cachedResultName": "Source_Folder"
          }
        },
        "options": {}
      },
      "type": "n8n-nodes-base.googleDrive",
      "typeVersion": 3,
      "position": [
        448,
        -96
      ],
      "id": "795209e8-d94e-461a-9db9-dad67e7541e3",
      "name": "Search files and folders"
    },
    {
      "parameters": {
        "operation": "download",
        "fileId": {
          "__rl": true,
          "value": "={{ $json.id }}",
          "mode": "id"
        },
        "options": {}
      },
      "type": "n8n-nodes-base.googleDrive",
      "typeVersion": 3,
      "position": [
        672,
        -96
      ],
      "id": "a0b81ff2-36f4-4e74-ac6d-0190e1663cb3",
      "name": "Download file"
    },
    {
      "parameters": {
        "driveId": {
          "__rl": true,
          "value": "My Drive",
          "mode": "list",
          "cachedResultName": "My Drive"
        },
        "folderId": {
          "__rl": true,
          "value": "YOUR_TARGET_FOLDER_ID",
          "mode": "id"
        },
        "options": {}
      },
      "type": "n8n-nodes-base.googleDrive",
      "typeVersion": 3,
      "position": [
        1232,
        -96
      ],
      "id": "8f4e2a35-4218-48ad-8535-19e412319d1a",
      "name": "Upload file"
    },
    {
      "parameters": {
        "chatId": "={{ $('Telegram Trigger').first().json.message.chat.id }}",
        "text": "=<i>Файлы успешно отправлены в папку Ready_to_Post✅</i>",
        "additionalFields": {
          "appendAttribution": false,
          "parse_mode": "HTML"
        }
      },
      "type": "n8n-nodes-base.telegram",
      "typeVersion": 1.2,
      "position": [
        1456,
        -96
      ],
      "id": "745b869f-64c0-45ec-b255-09de7cc6429a",
      "name": "Send a text message"
    }
  ],
  "pinData": {},
  "connections": {
    "Telegram Trigger": {
      "main": [
        [
          {
            "node": "If",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "If": {
      "main": [
        [
          {
            "node": "Search files and folders",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Search files and folders": {
      "main": [
        [
          {
            "node": "Download file",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Download file": {
      "main": [
        [
          {
            "node": "Aggregate",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Aggregate": {
      "main": [
        [
          {
            "node": "Compression",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Compression": {
      "main": [
        [
          {
            "node": "Upload file",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Upload file": {
      "main": [
        [
          {
            "node": "Send a text message",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  },
  "active": false,
  "settings": {
    "executionOrder": "v1",
    "binaryMode": "separate",
    "timezone": "UTC"
  },
  "meta": {
    "templateCredsSetupCompleted": false
  },
  "tags": []
}