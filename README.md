# Project Overview

This React App aims at using the ProPublica API to allow users and voters to easily access information about their House Representatives and Senate Members, as well as direct links to articles, contact forms, and involvement in various Bills.

​ 
![VOTE Smart](https://s.yimg.com/ny/api/res/1.2/3NaIFgeWkaXIf91cfAKjGQ--~A/YXBwaWQ9aGlnaGxhbmRlcjtzbT0xO3c9ODAw/http://media.zenfs.com/en-US/homerun/cosmopolitan_438/5eb0e5bbd540b600ee183bfb0bddc529)

## Project Links


- [Github Repo](https://github.com/tflosse/Congress-React-App)

- [Github Page] - (Not yet deployed)


## Project Description

This React App will feature lists of Congress members, both house and Senate along with basic information about their roles and direct links to their contact forms and recent Time magazine articles when available, and Bills they have introduced.

The goal of this app is to increase political engagement by making this information more accessible and easier to navigate by voters.​

#### User stories:
- Users can quickly find the House or Senate Representatives by State
- Users have one point of access for all useful links, including contact forms to write their representatives when available
- Users can quickly scan through introduced Bills, and get more details via a link to the Congress page.
- Users are encouraged to take action by sharing links and Congress member information.

#### Wireframes

![Simplified Wireframes](https://i.imgur.com/pcnVlyp.png)

#### Single-page Architecture

![React architecture & API Calls](https://i.imgur.com/NXX21Sv.png)

#### MVP OBJECTIVES

- Render a list of Congress members by State with a Senate/House toggle
- Search bar is to filter by State by default
- Member details render on load, bill details render on click
- Conditional rendering of links to Rep. external pages only when available
    - Rep. website
    - Contact form
    - Related Times Magazine articles
    - Twitter Account or Social media accounts

![Contact Kamala](https://i.imgur.com/UXvHJg0.png)
![Diane Feinstein in Times](https://i.imgur.com/Y543sxM.png)

#### PostMVP OBJECTIVES

- Form collects Name and Zipcode to track and measure geographical engagment
    - Will require building a simple backend to post and query data.
- Users sign up for a Rep "Watch-list"
    - Will require user auth and notification features

### The API

ProPublica's Congress API allows developers to retrieve legislative data from the House of Representatives, the Senate and the Library of Congress. The API, which originated at The New York Times in 2009, includes details about members, votes, bills, nominations and other aspects of congressional activity.

ProPublica is an independent nonprofit newsroom that aims to produce investigative journalism in the public interest. In 2010, it became the first online news source to win a Pulitzer Prize.

    Accessible data includes:

    - Member data: Along with general biographical information for current and past members of Congress, the API returns data about members’ Congressional roles. 
    Role data includes the Congress number and chamber, as well as the member’s title, state and party. 
    A single member may have more than one role in a particular Congress (for example, the member may switch parties or move from the House to the Senate). 
    Member data is available for every member who has served in Congress, with more information for those who have served since 1995.
    - Bill data: Along with standard bill summaries and details, the API returns bill subjects, amendments and related bills.
    - Lobbying data: The API returns data about filings from registered lobbyists.

More details about this API can be found [here](https://projects.propublica.org/api-docs/congress-api/).


### Time Invested

Unless otherwise noted, time is listed in hours:

| Component | Priority | Estimated Time | Time Invetsted |
| --- | :---: |  :---: | :---: |
| Project research and planning| H | 4 | 2 |
| Project write-up for approval | H | 2 | 1 |
| Wireframe design | M | 1 | 1 |
| React architecture & components | H | 1 | 30min |
| Navbar and mobile nav| H | 2 | 2 |
| Inspect API data structure | H | 2 | 3 |
| Make and test API calls | H | 2 | 2 |
| Search by State with Senate/House toggle | H | 4 | 4 |
| Render Search Results | H | 2 | 2 |
| Style the lists and buttons | H | 1 | 30min |
| Buttons for members, onClick | H | 2 | 1 |
| Member details page, including styling | H | 4 | 4 |
| Conditional logic for displaying external links | M | 2 | INC |
| Render Bills list with Hide/Show toggle | H | 2 | 3 |
| Style Bills list | M | 2 | 2 |
| Add content on About and Home pages | M | 1 | 1 |
| Create sharing links | M | 4 | INC |
| User Form on Homepage | L | 4 | INC |
| Add Loading... bar to bills display | L | 4 | INC |
| Styling for Media Queries | H | 6 | 4 |
| Total | H | 52 | INC |

### Code Snippets

Used states to bypass an issue with data type, and useEffect to make the API call when details page loads:

```js
function MemberDetails(props) {

const [memberDetails, updateMemberDetails]= useState({})
const [memberRole, setMemberRole]= useState({})
useEffect(() => {
    const memberApiCall = async () => {
      const res = await fetch(`https://api.propublica.org/congress/v1/members/${props.match.params.id}.json`,
      {
        dataType: "json",
        headers: { "X-API-Key": "ZuPHJPB5SdYllQS7KY4cAVoLY6mdboxoc3nZLOcE" }
      })
      const json = await res.json()
      let resultsObject = json.results[0]
      let role = json.results[0].roles[0]
      updateMemberDetails(resultsObject)
      setMemberRole(role)
    }
    memberApiCall()
  }, []); 
  return (
      <div className="member-info">
        <h3 id="member-name">{memberRole.short_title} {memberDetails.first_name} {memberDetails.last_name}</h3>
        <p>{currentParty(memberDetails.current_party)}<br />
        In role since: {memberRole.start_date}<br />
        Next election: {memberRole.next_election}</p>
      </div>
    )
};
```

Used states to show/hide details sections:

```js
    const [billsButton, setBillsButton]= useState('display')
    const [billsDiv, setBillsDiv]= useState('hidden')
    const handleShow = () => {
        console.log('Expanding Bills Section');
        billsApiCall()
        setBillsButton('hidden')
        setBillsDiv('shown')
    }; 
    const handleHide = () => {
        console.log('Hiding Bills Section')
        setBillsButton('display')
        setBillsDiv('hidden')
    };
    return (
      <div className="member-bills">
          <button className={`${billsButton}`} onClick={handleShow}>Show Sponsored Bills</button>
          <button className={`${billsDiv}`} onClick={handleHide}>Hide Sponsored Bills</button>
      </div>
    )
```

Used ternary to avoid rendering undefined elements:

```js
{stateResults?
              <SearchResults {...routerProps} 
                 stateResults={stateResults}
                 stateSearch={stateSearch}/>:<h4>{"Please enter a valid two-letter state abbriviation."}</h4>}

(...)

<p className="bill-summary">{bill.summary? `${bill.summary}`:"Bill Summary Unavailable"}</p>
```

#### API calls and response examples:

```js
    // Example API Call
        fetch("https://api.propublica.org/congress/v1/members/H001075.json", 
        {
        dataType: "json",
        headers: { "X-API-Key": "ZuPHJPB5SdYllQS7KY4cAVoLY6mdboxoc3nZLOcE" }
        })
```

```json

Object {status: "OK", copyright: "Copyright (c) 2020 Pro Publica Inc. All Rights Reserved.", results: Array[1]}

results: Array[1]
0: Object
id: "H001075"
member_id: "H001075"
first_name: "Kamala"
middle_name: null
last_name: "Harris"
suffix: null
date_of_birth: "1964-10-20"
gender: "F"
url: "https://www.harris.senate.gov"
votesmart_id: "120012"
twitter_account: "SenKamalaHarris"
facebook_account: "SenatorKamalaHarris"
google_entity_id: "/m/08sry2"
in_office: true
current_party: "D"
most_recent_vote: "2020-06-25"
last_updated: "2020-06-26 09:49:38 -0400"
roles: Array[2]
0: Object
1: Object
congress: "115"
chamber: "Senate"
title: "Senator, 3rd Class"
short_title: "Sen."
state: "CA"
(...)
office: "112 Hart Senate Office Building"
phone: "202-224-3553"
contact_form: "https://www.harris.senate.gov/content/contact-senator"
next_election: "2022"
bills_sponsored: 52
bills_cosponsored: 396
votes_with_party_pct: 85.71
votes_against_party_pct: 14.29
committees: Array[5]
subcommittees: Array[8]
```

