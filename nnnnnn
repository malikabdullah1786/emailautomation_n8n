{
  "nodes": [
    {
      "parameters": {
        "triggerTimes": {
          "item": [
            {
              "mode": "everyMinute"
            }
          ]
        }
      },
      "id": "0c5ba2d7-87bf-45fc-8e73-78b678520f29",
      "name": "Daily Email Trigger",
      "type": "n8n-nodes-base.cron",
      "typeVersion": 1,
      "position": [-1696, 64],
      "alwaysOutputData": true,
      "retryOnFail": true
    },
    {
      "parameters": {
        "operation": "getAll",
        "documentId": "1oFXvHS8iu56fU1OjesQEGfokhAIVVHcUV2i1vCp-NGY",
        "sheetName": "Sheet1",
        "options": {}
      },
      "id": "1ebc9fa7-3611-47b0-9d5a-c7cf34784d01",
      "name": "Get Email List with Website URLs",
      "type": "n8n-nodes-base.googleSheets",
      "typeVersion": 4,
      "position": [-1488, 64],
      "alwaysOutputData": true,
      "retryOnFail": true
    },
    {
      "parameters": {
        "conditions": {
          "options": {
            "caseSensitive": true,
            "leftValue": "",
            "typeValidation": "strict",
            "version": 1
          },
          "conditions": [
            {
              "id": "filter-unsent",
              "leftValue": "={{ $json.status }}",
              "rightValue": "unsent",
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
      "id": "9921a658-0333-4d27-9ba0-e05373f3aebd",
      "name": "Filter Unsent Emails",
      "type": "n8n-nodes-base.filter",
      "typeVersion": 2,
      "position": [-1264, 64]
    },
    {
      "parameters": {
        "maxItems": 200
      },
      "id": "49afd5c9-a857-4020-8307-4cd1827c3ff6",
      "name": "Limit to 200 Emails",
      "type": "n8n-nodes-base.limit",
      "typeVersion": 1,
      "position": [-1040, 64],
      "alwaysOutputData": true,
      "retryOnFail": true
    },
    {
      "parameters": {
        "options": {}
      },
      "type": "n8n-nodes-base.splitInBatches",
      "typeVersion": 3,
      "position": [-864, 64],
      "id": "060abec3-6298-4364-871c-c35da8ea615e",
      "name": "Loop Over Items"
    },
    {
      "parameters": {
        "url": "={{ $json.website_url }}",
        "options": {
          "timeout": 10000,
          "redirect": {
            "redirect": {}
          },
          "response": {
            "response": {
              "responseFormat": "text"
            }
          }
        }
      },
      "id": "website-fetcher",
      "name": "Fetch Website Content",
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 4.2,
      "position": [-688, 64],
      "continueOnFail": true,
      "alwaysOutputData": true
    },
    {
      "parameters": {
        "mode": "runOnceForEachItem",
        "jsCode": "// Extract website data from HTML\nconst html = $json.data || '';\nconst cheerio = require('cheerio');\n\ntry {\n  const $ = cheerio.load(html);\n  \n  // Extract company information\n  const companyName = $('title').text().split('-')[0].trim() || \n                     $('h1').first().text().trim() || \n                     'the company';\n  \n  const metaDescription = $('meta[name=\"description\"]').attr('content') || \n                         $('meta[property=\"og:description\"]').attr('content') || \n                         '';\n  \n  // Extract industry keywords from content\n  const bodyText = $('body').text().toLowerCase();\n  const industryKeywords = [];\n  \n  const industries = {\n    'technology': ['software', 'tech', 'digital', 'app', 'platform', 'saas', 'cloud'],\n    'healthcare': ['health', 'medical', 'hospital', 'clinic', 'pharmaceutical', 'wellness'],\n    'finance': ['finance', 'banking', 'investment', 'insurance', 'financial', 'accounting'],\n    'retail': ['retail', 'ecommerce', 'shop', 'store', 'marketplace', 'shopping'],\n    'education': ['education', 'school', 'university', 'training', 'learning', 'academy'],\n    'consulting': ['consulting', 'advisory', 'professional services', 'strategy', 'solutions'],\n    'manufacturing': ['manufacturing', 'production', 'factory', 'industrial', 'equipment']\n  };\n  \n  for (const [industry, keywords] of Object.entries(industries)) {\n    if (keywords.some(keyword => bodyText.includes(keyword))) {\n      industryKeywords.push(industry);\n    }\n  }\n  \n  // Extract recent news/blog content\n  const recentContent = $('article h2, .blog-title, .news-title').first().text().trim() || \n                        $('h2, h3').filter(':contains(\"news\"), :contains(\"blog\"), :contains(\"update\")').first().text().trim();\n  \n  // Extract contact information\n  const phoneMatch = bodyText.match(/(\\+?1?[\\s.-]?\\(?\\d{3}\\)?[\\s.-]?\\d{3}[\\s.-]?\\d{4})/);\n  const emailMatch = bodyText.match(/([a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,})/);\n  \n  // Extract services/products\n  const services = [];\n  const serviceKeywords = $('nav a, .services a, .menu a').map((i, el) => $(el).text().trim()).get();\n  \n  return {\n    json: {\n      ...($json || {}),\n      websiteData: {\n        companyName: companyName,\n        metaDescription: metaDescription,\n        industry: industryKeywords.join(', ') || 'business',\n        recentContent: recentContent,\n        extractedPhone: phoneMatch ? phoneMatch[0] : null,\n        extractedEmail: emailMatch ? emailMatch[0] : null,\n        services: serviceKeywords.slice(0, 5), // Top 5 services\n        hasContent: html.length > 0\n      }\n    }\n  };\n} catch (error) {\n  return {\n    json: {\n      ...($json || {}),\n      websiteData: {\n        companyName: 'the company',\n        metaDescription: '',\n        industry: 'business',\n        recentContent: '',\n        extractedPhone: null,\n        extractedEmail: null,\n        services: [],\n        hasContent: false,\n        error: error.message\n      }\n    }\n  };\n}"
      },
      "id": "website-data-extractor",
      "name": "Extract Website Data",
      "type": "n8n-nodes-base.code",
      "typeVersion": 2,
      "position": [-496, 64]
    },
    {
      "parameters": {
        "mode": "runOnceForEachItem",
        "jsCode": "// Create personalized email content based on website data\nconst currentDateTime = new Date().toISOString();\nconst websiteData = $json.websiteData || {};\nconst recipientName = $json.name || 'there';\nconst companyName = websiteData.companyName || 'your company';\nconst industry = websiteData.industry || 'your industry';\nconst recentContent = websiteData.recentContent;\n\n// Dynamic subject lines based on industry\nconst subjectTemplates = {\n  'technology': `Scaling ${companyName}'s Digital Growth`,\n  'healthcare': `Healthcare Innovation Opportunities for ${companyName}`,\n  'finance': `Financial Technology Solutions for ${companyName}`,\n  'retail': `E-commerce Growth Strategies for ${companyName}`,\n  'education': `Educational Technology Enhancement for ${companyName}`,\n  'consulting': `Strategic Partnership Opportunity with ${companyName}`,\n  'default': `Partnership Opportunity with ${companyName}`\n};\n\n// Get subject based on detected industry\nconst getSubject = () => {\n  for (const [key, template] of Object.entries(subjectTemplates)) {\n    if (industry.includes(key)) {\n      return template;\n    }\n  }\n  return subjectTemplates.default;\n};\n\n// Personalized email body\nconst createEmailBody = () => {\n  let greeting = `Hi ${recipientName},\\n\\n`;\n  \n  let intro = `I hope this email finds you well. `;\n  \n  // Add company-specific context\n  if (websiteData.hasContent) {\n    if (websiteData.metaDescription) {\n      intro += `I came across ${companyName} and was impressed by your work in ${industry}. `;\n    } else {\n      intro += `I've been researching companies like ${companyName} in the ${industry} space. `;\n    }\n  } else {\n    intro += `I wanted to reach out regarding potential collaboration opportunities. `;\n  }\n  \n  // Add recent content reference if available\n  let contentReference = '';\n  if (recentContent) {\n    contentReference = `I noticed your recent focus on \"${recentContent}\" - this aligns perfectly with some innovative solutions we've been developing. \\n\\n`;\n  }\n  \n  // Industry-specific value proposition\n  let valueProposition = '';\n  if (industry.includes('technology')) {\n    valueProposition = `We've helped similar tech companies increase their development efficiency by 40% through our automated solutions. `;\n  } else if (industry.includes('healthcare')) {\n    valueProposition = `We specialize in healthcare technology solutions that have helped medical organizations improve patient engagement by up to 60%. `;\n  } else if (industry.includes('finance')) {\n    valueProposition = `Our fintech solutions have helped financial institutions reduce processing time by 50% while maintaining compliance. `;\n  } else if (industry.includes('retail')) {\n    valueProposition = `We've assisted retail companies in boosting online sales by an average of 35% through targeted automation strategies. `;\n  } else {\n    valueProposition = `We've helped companies in various industries streamline their operations and achieve measurable growth. `;\n  }\n  \n  let callToAction = `Would you be available for a brief 15-minute call this week to explore how we could potentially collaborate?`;\n  \n  let signature = `\\n\\nBest regards,\\nMuhammad Abdullah\\nFAST\\n0309-4561786\\n\\nP.S. I'd be happy to share some case studies from companies similar to ${companyName} if you're interested.`;\n  \n  return greeting + intro + contentReference + valueProposition + callToAction + signature;\n};\n\nreturn {\n  json: {\n    email: $json.email,\n    name: $json.name,\n    row_number: $json.row_number,\n    subject: getSubject(),\n    body: createEmailBody(),\n    send_time: currentDateTime,\n    follow_up_count: $json.follow_up_count || 0,\n    status: 'sending',\n    websiteData: websiteData,\n    personalization_score: websiteData.hasContent ? 'high' : 'medium'\n  }\n};\n"
      },
      "id": "personalized-email-creator",
      "name": "Create Personalized Email",
      "type": "n8n-nodes-base.code",
      "typeVersion": 2,
      "position": [-304, 64]
    },
    {
      "parameters": {
        "resource": "message",
        "subject": "={{ $json.subject }}",
        "message": "={{ $json.body }}",
        "toList": [
          "={{ $json.email }}"
        ],
        "additionalFields": {}
      },
      "id": "d2dec755-9012-4bda-a6a7-48090c0f87ce",
      "name": "Send Personalized Email",
      "type": "n8n-nodes-base.gmail",
      "typeVersion": 1,
      "position": [-112, 64],
      "alwaysOutputData": true,
      "retryOnFail": false,
      "continueOnFail": true,
      "credentials": {
        "gmailOAuth2": {
          "id": "nhrygbAi2d4XyJHY",
          "name": "Gmail account"
        }
      }
    },
    {
      "parameters": {
        "operation": "update",
        "documentId": "1AeahKOLAtn5TOhJ_8IGDIcyWDOZ2frOGs2Jx8_nUWiw",
        "sheetName": "Sheet1",
        "columns": {
          "mappingMode": "defineBelow",
          "value": {
            "row_number": "={{ $json.row_number }}",
            "Recipient": "={{ $json.email }}",
            "Company": "={{ $json.websiteData.companyName }}",
            "Industry": "={{ $json.websiteData.industry }}",
            "Sent Date": "={{ $json.send_time }}",
            "Message ID": "={{ $json.threadId }}",
            "Subject": "={{ $json.subject }}",
            "Personalization Score": "={{ $json.personalization_score }}",
            "Website Data Extracted": "={{ $json.websiteData.hasContent }}",
            "Follow-up Count": "={{ $json.follow_up_count }}"
          },
          "matchingColumns": ["row_number"]
        },
        "options": {}
      },
      "id": "enhanced-tracking",
      "name": "Log Enhanced Email Tracking",
      "type": "n8n-nodes-base.googleSheets",
      "typeVersion": 4,
      "position": [80, 64],
      "credentials": {
        "googleSheetsOAuth2Api": {
          "id": "Nri9hzDHGc2ECAU9",
          "name": "Google Sheets account 2"
        }
      }
    },
    {
      "parameters": {},
      "type": "n8n-nodes-base.wait",
      "typeVersion": 1.1,
      "position": [272, 64],
      "id": "43baf5ee-2403-47c0-baad-92f6b8c09903",
      "name": "Wait Between Emails"
    }
  ],
  "connections": {
    "Daily Email Trigger": {
      "main": [
        [
          {
            "node": "Get Email List with Website URLs",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Get Email List with Website URLs": {
      "main": [
        [
          {
            "node": "Filter Unsent Emails",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Filter Unsent Emails": {
      "main": [
        [
          {
            "node": "Limit to 200 Emails",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Limit to 200 Emails": {
      "main": [
        [
          {
            "node": "Loop Over Items",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Loop Over Items": {
      "main": [
        [
          {
            "node": "Fetch Website Content",
            "type": "main",
            "index": 0
          }
        ],
        []
      ]
    },
    "Fetch Website Content": {
      "main": [
        [
          {
            "node": "Extract Website Data",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Extract Website Data": {
      "main": [
        [
          {
            "node": "Create Personalized Email",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Create Personalized Email": {
      "main": [
        [
          {
            "node": "Send Personalized Email",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Send Personalized Email": {
      "main": [
        [
          {
            "node": "Log Enhanced Email Tracking",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Log Enhanced Email Tracking": {
      "main": [
        [
          {
            "node": "Wait Between Emails",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Wait Between Emails": {
      "main": [
        [
          {
            "node": "Loop Over Items",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  }
}