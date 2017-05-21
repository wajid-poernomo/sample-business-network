# sample-business-network

The .bna contains a very simple asset and participant, with the following ACL:

rule Default {
    description: "Allow all participants access to all resources"
    participant: "org.acme.sample.SampleParticipant"
    operation: READ
    resource: "org.acme.sample.SampleAsset"
    action: DENY
}

Verify ACL works for a simple asset and participant:

1. Use docker-compose (scripts/setup.sh) found in getting-started to create a hyperledger fabric.

2. Deploy .bna file to running fabric:
	composer network deploy --archiveFile  basic-sample-network.bna  --enrollId WebAppAdmin --enrollSecret DJY27pEnl16d

3. Create a SampleParticipant and issue an identity:

composer participant add -n 'basic-sample-network' -i admin -s Xurw3yU9zI0l -d '{"$class":"org.acme.sample.SampleParticipant", "participantId":"participantId:2017", "firstName":"Joe", "lastName":"Blogs" }'

composer identity issue -n 'basic-sample-network' -i admin -s Xurw3yU9zI0l -u joeblogs1 -a "org.acme.sample.SampleParticipant#participantId:2017"

4. Generate REST server using the above fabric and deployed network:

composer-rest-server -n basic-sample-network -p defaultProfile -i WebAppAdmin -s DJY27pEnl16d -N never -P 3000

5. Add an Sample Asset using the explorer.

6. Generate REST server using the enrollmentID and enrollment secret for the participant added previously (secret will be different):

composer-rest-server -n basic-sample-network -p defaultProfile -i joeblogs1 -s meIhSMgbUbSp -N never -P 3000

7. Try to get the asset created in (5). Since the user for this rest server is an SampleParticipant, the acl shoudl deny read access.
