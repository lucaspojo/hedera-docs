# QueryHeader

Each query from the client to the node will contain the QueryHeader, which gives the requested response type, and includes a payment for the response. It will sometimes leave payment blank: it is blank for TransactionGetReceiptQuery. It can also be left blank when the responseType is costAnswer or costAnswerStateProof. But it needs to be filled in for all other cases. The idea is that an answer that is only a few bytes \(or that was paid for earlier\) can be given for free. But if the answer is something that requires many bytes or much computation \(like a state proof\), then it should be paid for.

| Field | Type | Description |  |
| :--- | :--- | :--- | :--- |
| payment | [Transaction](transaction.md#transaction) | A signed CryptoTransferTransaction to pay the node a fee for handling this query |  |
| responseType | [ResponseType](query.md#query) | The requested response, asking for cost, state proof, both, or neither |  |

## ResponseType

The client uses the ResponseType to request that the node send it just the answer, or both the answer and a state proof. It can also ask for just the cost for getting the answer or both. If the payment in the query fails the precheck, then the response may have some fields blank. The state proof is only available for some types of information. It is available for a Record, but not a receipt. It is available for the information in each kind of \*GetInfo request.

| Enum Name | Description |
| :--- | :--- |
| ANSWER\_ONLY | Response returns answer |
| ANSWER\_STATE\_PROOF | Response returns both answer and state proof \(not yet supported\) |
| COST\_ANSWER | Response returns the cost of answer |
| COST\_ANSWER\_STATE\_PROOF | Response returns the total cost of answer and state proof \(not yet supported\) |

