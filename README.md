# Scrape
'''Tweet Streaming API consumer'''
import twitter, json, sys, csv 

# == OAuth Authentication ==
consumer_key="9FwthcKRdHimeXt9ANnWGrupt"
consumer_secret="RdDlY6GZSiyK7bVoovsNaVpMoYZhbZCqn0Axib2Rc4vDhjhhCP"
access_token="60318094-sUaXHyz6O0Qr25wFC8za7lF5wduEMyFrNxdlpclH4"
access_token_secret="7F7UOLDmLKFBoSTcvucWC9NiW5h8I21jF3TooNrt8g39l"


auth = twitter.oauth.OAuth(access_token, access_token_secret, consumer_key, consumer_secret)
twitter_api = twitter.Twitter(auth=auth)

csvfile = open('oddeven.csv', 'w')
csvwriter = csv.writer(csvfile)
csvwriter.writerow(['created_at',
                    'user-screen_name',
                    'text',
                    'coordinates lng',
                    'coordinates lat',
                    'place',
                    'user-location',
                    'user-geo_enabled',
                    'user-lang',
                    'user-time_zone',
                    'user-statuses_count',
                    'user-followers_count',
                    'user-created_at'
                    ])

q = "#oddeven"
print 'Filtering the public timeline for keyword="%s"' % (q)
twitter_stream = twitter.TwitterStream(auth=twitter_api.auth)
stream = twitter_stream.statuses.filter(track=q)


# helper functions, clean data, unpack dictionaries
def getVal(val):
    clean = ""
    if isinstance(val, bool):
        return val
    if isinstance(val, int):
        return val
    if val:
        clean = val.encode('utf-8') 
    return clean

def getLng(val):
    if isinstance(val, dict):
        return val['coordinates'][0]

def getLat(val):
    if isinstance(val, dict):
        return val['coordinates'][1]

def getPlace(val):
    if isinstance(val, dict):
        return val['full_name'].encode('utf-8')


# main loop
for tweet in stream:
    try:
        csvwriter.writerow([tweet['created_at'],
                            getVal(tweet['user']['screen_name']),
                            getVal(tweet['text']),
                            getLng(tweet['coordinates']),
                            getLat(tweet['coordinates']),
                            getPlace(tweet['place']),
                            getVal(tweet['user']['location']),
                            getVal(tweet['user']['geo_enabled']),
                            getVal(tweet['user']['lang']),
                            getVal(tweet['user']['time_zone']),
                            getVal(tweet['user']['statuses_count']),
                            getVal(tweet['user']['followers_count']),
                            getVal(tweet['user']['created_at'])
                            ])
        csvfile.flush()
        print tweet['user']['screen_name'].encode('utf-8'), tweet['text'].encode('utf-8')
    except Exception as e:
        print e 
