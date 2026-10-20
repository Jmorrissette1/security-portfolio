# [Title, outcome-flavored: "Investigating an Identity Attack in Entra ID"]

## Scenario

Reconstructed a five-stage OAuth consent-phishing kill chain in a live Azure tenant through forensic analysis of two linked app registrations.

## Environment

One list: platform, services, tools, access level. Honest framing: "live multi-user Azure training tenant, Reader access."

## Investigation

<ol>
<li>ENTRY. A user was phished, completed MFA, and had the resulting session token stolen. That token carried an MFA-satisfied claim, so it sailed past Conditional Access. That user was also, through years of drift, still an Owner on a legacy connector app.</li>

<li>ESCALATE. Using those Owner rights, the attacker minted a new client secret on the legacy app. That secret let them authenticate through the client credentials flow as the service principal itself, inheriting the app's directory permissions without ever signing in as a human again. Note the expiry date: set nearly a century out.</li>

<li>PIVOT. A single secret dies when it gets rotated. So the attacker registered their own app (every standard user can do this by default in Entra) and added its service principal to the legacy app's Owners list. Now they can re-credential the legacy app forever, even after the first secret is caught.</li>

<li>PERSIST. Then the backup plan: a custom scope published on the legacy app's Expose an API blade. This turns the legacy app into a callable backend resource, which means the attacker's own app can request delegated access to it.</li>

<li>LOOT. Finally, a redirect URI on the rogue app pointing at attacker-controlled infrastructure. Combining the rogue app's client ID, that redirect URI, and the exposed API scope produces a working phishing URL. A victim who is already signed in on a corporate device clicks Accept on a consent prompt, and the authorization code lands on the attacker's server.</li>
</ol>
## What broke / what surprised me

The most credible section in the document. Dead ends, wrong guesses, the thing that took an hour. Employers know real work is messy. This section separates you from certificate collectors.

## Findings and recommendations

What you determined, plus 2 or 3 recommendations as if you were reporting to the resource owner.

## What I learned

3 to 5 bullets. At least one technical, one "what I'd do differently."
